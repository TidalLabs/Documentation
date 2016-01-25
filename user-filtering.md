##################################
New User Filtering System
##################################

============================
Level 0 Filters - Same Table
============================

Same table filters work the same way they did previously. The lookup field, lookup comparison, and lookup value are specified in a numerically-indexed array.  Examples:

.. code-block:: php

   $filters = [
      ['gender', '=', 'f'],
      ['grade', '>', 70],
      ['city_name', 'like', 'York'],
      ['zipcode', 'in', ['10011', '11201', '90210']],
   ];

We refer to the three values in this array as the key, comparator, and value, and unpack them into ``$key``, ``$comparator``, and ``$value`` variables.

These filters are processed in a very straightforward manner, with a ``WHERE`` clause being added to the query:

.. code-block:: php

   $query->where($key, $comparator, $value);


OR

.. code-block:: php

   $query->whereIn($key, $value);

================================================
Level 0.5 Filters - Related Table, Simple Lookup
================================================

Simple related table lookups also still work as they did previously.  A dot notation should be used for table/field; tables that may be used are aliased and whitelisted in ``Tidal\Utilities\QueryFilters``.

.. code-block:: php

   $filters = [
      ['blogs.traffic_estimate', '>', 10000],
   ];

In these cases the ``$key`` is tested against the whitelisted aliases, and if one matches, a join is generated to the aliased table (in the example above, the ``user_blogs`` table.  Then a ``WHERE`` condition is added as above.


=======================
Level 0 Special Filters
=======================

There are a few specially-defined lookups in ``Tidal\Utilities\QueryFilters::runMacros()``. If the $key in the filter definition matches one of these specially defined lookups, normal ``WHERE`` clause generation will be short-circuited in favor of additional processing and possible multiple ``WHERE`` condition generation.

These cases are ones where the specific processing is not very generalizable.  In particular, the 'query' and 'location' lookups need to search multiple fields (such as name, email, and blog in the former; and city, state, and country in the latter).

============================
Representing Complex Filters
============================

More complex filters are defined in the back end (currently in ``application\config\filters.php``), so that

#. We can define them as multidimensional arrays instead of having to parse strings to figure out what we need
#. We don't have to trust user input.

A complex filter definition's key starts with an underscore to indicate that it **is** a complex filter and should be looked up in the filters array.  A number of keys are available for the definition, and the use of each will be described below.

It's also worth noting that in a join, a complex filter's table will be aliased with a name based on the filter definition's key.  This is so that filters don't stomp on one another.  For instance, if you want to filter on both miniumum Twitter followers and minimum Instagram followers, you can't do both in a single join -- because each join needs to limit rows retrieved to those for a single network.

=====================================================
Level 1-2 Filters - Related Table, Conditional Lookup
=====================================================

Some related filters require a conditional lookup.  For instance, the ``user_socials`` table stores social account data for all social media types, including Twitter, Facebook, Instagram, Pinterest, YouTube, etc.  If we just want to retrieve users with a minimum of Twitter followers, we need to make sure we're looking at only those rows keyed to Twitter.

Our complex filter definition might look something like this:

.. code-block:: php

   $filtersDefs['users'] = [
      '_twitter_followers' => [
         'table' => 'user_socials',
         'field' => 'followers_count',
         'joinConditions' => [
            ['type', '=', 'twitter'],
         ],
      ],
   ];

This tells us that:

* We're filtering on related data in the ``user_socials`` table
* The field we're filtering against is the ``followers_count`` field (i.e. our ``WHERE`` clause should be defined using ``$query->where('followers_count', $comparator, $value);``).
* Based on the ``joinConditions``, our join should be not just on ``users.id=user_socials.user_id`` but rather on ``users.id=user_socials.user_id AND user_socials.type='twitter'``

This system also supports multiple conditions in a single join.  For instance, if you wanted to find users with a certain number of Instagram followers, but only in cases where the Instagram handle was entered by the user (NOT ones where we guessed at the handle based on the Twitter handle), you might do this:

.. code-block:: php

   $filtersDefs['users'] = [
      '_instagram_followers' => [
         'table' => 'user_socials',
         'field' => 'followers_count',
         'joinConditions' => [
            ['type', '=', 'instagram'],
            ['inferred', '=', false],
         ],
      ],
   ];


===================================================
Level 3 Filters - Related Table, Variable Condition
===================================================

Filters are defined in code, at "compile time", and run with comparators and values submitted at runtime.  However, sometimes a filter definition (particularly a join condition) relies on values available only at run time.

For instance, user stats are in a table that includes stats for all channels, but we might want to retrieve stats for just the current channel on the ``/manage`` dash.

This is accomplished via interpolation.  Variables for interpolation are declared in the format ``{{var_name}}``.  Values for permitted interpolation variables are passed in at runtime to the RequestHelper (see below), which performs the interpolation.

For instance, a "Published Post Count" filter for the manage dash might be declared like this:

.. code-block:: php

   $filtersDefs['users'] = [
      '_published_posts' => [
         'table' => 'user_stats',
         'field' => 'value',
         'joinConditions' => [
            ['key', '=', 'published'],
            ['channel_id', '=', '{{channel_id}}'],
         ],
      ],
   ];

.. warning:: The above code is actually NOT complete because of casting issues.  See the next section for an explanation.


===========================
Level 3.5 Filters - Casting
===========================

We have a small problem with some tables, where numeric values are stored as strings.  That means that in order to perform meaningful filtering, we need to cast the values in the table.  This is particularly relevant in the user_stats table.

The filter definition for "Published Post Count" that we previously discussed should actually be written as below, to ensure that the ``WHERE`` clause is written as ``WHERE CAST(value as int) > 100`` rather than just ``WHERE value > 100)``, since the latter would return rows where the value is ``'50'``.

.. code-block:: php

   $filtersDefs['users'] = [
      '_published_posts' => [
         'table' => 'user_stats',
         'field' => 'value',
         'joinConditions' => [
            ['key', '=', 'published'],
            ['channel_id', '=', '{{channel_id}}'],
         ],
         'cast' => 'int',
      ],
   ];

Additionally, Eloquent and PDO are usually effective in casting bound variables when executing a parameterized query, but less so in a ``HAVING`` clause, for some reason.  Since all the values we compare against come through the request as strings, some need to be cast to integers before being bound.  In these cases, we can define a callback in the ``valueCast`` key to cast or otherwise process the incoming value:

.. code-block:: php

   $filtersDefs['users'] = [
      '_something' => [
         'table' => 'user_foo',
         'field' => 'bar_stat',
         'valueCast' => 'intval',
      ],
      '_something_else' => [
         'table' => 'user_baz',
         'field' => 'quux_stat',
         'valueCast' => [HelperClass::class, 'methodName'],
      ],
   ];

=================================================
Level 4 Filters - Related Table, Aggregate Lookup
=================================================

Sometimes we're looking for an aggregate value.  For instance, in newadmin, we show the sum of a user's published post counts across all channels, and the max of total posts associated with the account.  Or we might want to find users with a following of at least 10,000 on **any** social network; or a total following of at least 100,000 across all networks together

To do this, we need to use SQL aggregates.  This is very simple to add to the filter definition:

.. code-block:: php

   $filtersDefs['users'] = [
      '_best_social_following' => [
         'table' => 'user_socials',
         'field' => 'followers_count',
         'aggregate' => 'MAX',
         'valueCast' => 'intval',
      ],
      '_newadmin_published_posts' => [
         'table' => 'user_stats',
         'field' => 'value',
         'joinConditions' => [
            ['key', '=', 'published'],
         ],
         'cast' => 'int',
         'aggregate' => 'SUM',
         'valueCast' => 'intval',
      ],
   ];


It's worth noting that because of how aggregates work, if we filter by multiple aggregates in a single query, or both filter and sort by an aggregate, we end up with multiples of each record in the aggregated table, which completely throws the numbers off.  Instead, we perform aggregate filters in subqueries, and in the top level query, search for users who have IDs in the set returned by the subquery.  In PostgreSQL, this is very efficient, but my understanding is that it has problematic performance implications in MySQL, which will re-run the subquery for every row in the top-level query.

##############################
Using Complex Filters in Forms
##############################

Complex filters get added to form definitions in a manner very similar to how simple filters do.  You simply use ``_key_name:comparitor`` and ``_key_name:value`` for the comparitor and value fields.  You may need to use a hidden field with the comparitor type (``'='`` or ``'LIKE'``) for text inputs.  Here are some examples:

.. code-block:: php

   $formFields = [
      ['label' => '<strong>Twitter Handle</strong>', 'type' => 'text', 'name' => '_twitter_slug:value', 'placeholder' => 'Search...'],
      ['type' => 'hidden', 'name' => '_twitter_slug:comparitor','value' => 'LIKE',],
      ['label' => '<strong>Twitter Following</strong>', 'type' => 'select',
         'name' => '_twitter_followers:comparitor', 'default' => 'Select', 'options' => [
            ['value' => '>=', 'text' => 'Greater Than'],
            ['value' => '<=', 'text' => 'Less Than'],
      ]],
      ['type' => 'select', 'name' => '_twitter_followers:value', 'default' => 'Followers', 'options' => $trafficOptions],

   ];

#############
RequestHelper
#############

The RequestHelper class lets you use request data to apply sorting and filtering to a query in just a few lines of code instead of hundreds.  Some of the things it does for filters:

* Use the presence of leading underscores to determine whether a lookup is simple or complex
* Interpolate whitelisted variables into the filter definition
* Cast values that require casting
* Add ``'%'`` signs to lookup values for ``LIKE`` lookups
* Transform the filter definition to a format that can be used by the ``Tidal\Traits\QueryFilters::scopeAddFilter()`` method to actually apply the filter

=====
Usage
=====

Filter definitions need to be read from Kohana config and passed through when instantiating a RequestHelper instance:

.. code-block:: php

   $reqHelper = RequestHelper::factory(
      $request,
      \Tidal\User::class, // The model that we'll be searching for records
      \Gator\Helper\UserHelper::class, // A class that may define custom transformations
                                       // or filters that must be applied to every query.
                                       // Its `augmentFilters()` method will be permitted to make changes to the fully-processed filter list.
      Kohana::$config->load('filters.users'), // An array of filter definitions
      Kohana::$config->load('tables.users'), // An array of column definitions; not used for filtering.
                                             // If you will not be sorting, you can pass NULL here instead.
      ['channel_id' => (string) $this->channel->_id] // An array of variables that may be interpolated into sort and filter definitions.
   );
   $query = $reqHelper->makeQuery(['users.*']) // create a basic SELECT, specifying fields to include in the SELECT clause
      ->filterQuery()
      ->sortQuery(true) // the boolean argument indicates whether or not grouping needs to be applied
      ->getQuery
      ;
