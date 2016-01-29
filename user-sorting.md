# New User Sorting System

Table column definitions are stored in the ``config/tables.php`` or (for newadmin) ``config/newadmin_tables.php`` files.  Each table's definition includes column definitions as well as metadata that includes information such as what DB table is the primary table for the data (used for joins), and what sort order should be used if non is specified in the request.

Not all columns are sortable.  Any column that **is** sortable has a ``'sortBy'`` key in its column definition.  The ``sortBy`` key may point to either a string or array value.

## Level 0 Sorting - Same Table

Simple sorts can be represented by a single string containing the name of the DB field on which to sort.  For instance:

```
   $columns = [
      'display_name' => [
         'type' => 'text',
         'title' => 'Display Name',
         'requires' => ['display_name'],
         'use' => 'display_name',
         'sortBy' => 'display_name',
      ],
   ];
```

This will add a simple ``ORDER BY display_name`` clause to the query.

Alternatively, the field to sort on can be represented using the mandatory ``'sortField'`` key in a ``sortBy`` array. The below would result in the same sort order as the above:

```
   $columns = [
      'display_name' => [
         'type' => 'text',
         'title' => 'Display Name',
         'requires' => ['display_name'],
         'use' => 'display_name',
         'sortBy' => [
            'sortField' => 'display_name',
         ],
      ],
   ];
```

## Level 0.5 Sorting - Coalesce, Reverse Sort

### Coalesce

Data isn't always clean, and a lot of DB fields contain nulls.  Unfortunately, nulls always seem to sort into exactly the last place in the list where you want them, for instance at the end of a list of integers sorted in ascending order, or sandwiching non-empty string values between a list of empty strings and a list of nulls.

This can be worked around by forcing nulls to coalesce to a more sensible value -- such as 0 or 1 for integers or an empty string for strings.  We express our coalesce directive like so:

```
   $columns = [
      'grade' => [
         'type' => 'text',
         'title' => 'Grade',
         'requires' => ['human_grade'],
         'use' => 'human_grade',
         'sortBy' => [
            'sortField' => 'grade',
            'coalesceTo' => -1,
         ],
      ],
   ];
```

The above translates to an ``ORDER BY COALESCE(grade, -1)`` clause, forcing any records with null ``grade`` fields to the end of a list sorted in descending order.

### Reverse Sort

Some calculated fields have values that should be displayed in reverse order from the underlying data.  In particular, "age" always increases as birthdate decreases.  We can declare a reverse sort in the sorting directive, and this will ensure that when the user clicks the "ascending sort" arrow, that arrow is what will be highlighted, and the underlying data will be sorted in descending order so that the calculated data will appear in ascending order.

```
   $columns = [
      'age' => [
         'type' => 'number',
         'title' => 'Age',
         'requires' => ['age'],
         'use' => 'age',
         'sortBy' => [
            'sortField' => 'birthday',
            'reverseSort' => true,
            'coalesceTo' => '3000-01-01', // far future date
         ],
      ],
   ];
```

Note that with a reverse sort, you may want to coalesce null raw data to a high rather than a low value if you wish to have those rows appear alongside the low calculated values.

## Level 1 Sorting - Related Table, Simple Lookup

To sort by a field in a related table, the related table must be specified.  For instance, the following sorts by the related ``user_blogs.traffic_estimate`` field:

```
   $columns = [
      'blog_traffic' => [
         'type' => 'number',
         'title' => 'Blog Traffic',
         'requires' => ['blog.traffic_estimate'],
         'use' => 'blog.traffic_estimate',
         'sortBy' => [
            'table' => 'user_blogs',
            'sortField' => 'traffic_estimate',
            'coalesceTo' => -1,
         ],
      ],
   ];
```

Note that since we want to continue to include users with no blog, the join between the ``users`` table and the ``user_blogs`` table is performed as a ``LEFT JOIN``.  Users with no related blog record will have a NULL traffic_estimate field, and because we're using ``COALESCE``, they'll show at the end of descending sort; however, their traffic numbers will still show as null or zero, because the ``COALESCE`` appliees only to the ordering clause, not the select.

The related table join for sorting purposes is also always aliased (we call it ``"_sortByRelation"``) so that it doesn't interfere with any joins to the same table for filtering purposes (which is especially a important when the sort requires a conditional join or an aggregation; we'll explain how to define conditional join sorts and aggregate sorts below).

## Level 2 Sorting - Related Table, Conditional Lookup

Some sorting requires a conditional lookup.  For instance, the ``user_socials`` table stores social account data for all social media types, including Twitter, Facebook, Instagram, Pinterest, YouTube, etc.  If we want to sort by number of Twitter followers, we need to make sure we're looking at only those related rows keyed to Twitter (i.e. where the ``type`` field contains ``'twitter'``..

We do this by adding conditions to the join.  It is very important that these conditions be on the join, rather than in a ``WHERE`` clause, because in a ``WHERE`` clause they would entirely filter out users with no Twitter account.

```
   $columns = [
      'twitter_followers' => [
         'type' => 'number',
         'title' => 'Twitter Followers',
         'requires' => ['twitter.followers_count'],
         'use' => 'twitter.followers_count',
         'sortBy' => [
            'table' => 'user_socials',
            'sortField' => 'followers_count',
            'coalesceTo' => -1,
            'join' => [
               'conditions' => [
                  [
                     'field' => 'type',
                     'comparitor' => '=',
                     'value' => 'twitter',
                  ],
               ]
            ],
         ],
      ],
   ];
```

A condition that specifies a ``field`` and ``value`` but no ``comparitor`` will be treated as having specified ``'comparitor' => '='``.

A conditional sort can specify multiple conditions -- for instance, if we wanted to sort based on the number of posts they'd published on the TODAY channel, we might do something like:

```
   $columns = [
      'posts_published' => [
         'type' => 'number',
         'title' => 'Posts Published',
         'requires' => ['published_posts'],
         'use' => 'published_posts',
         'sortBy' => [
            'table' => 'user_stats',
            'sortField' => 'value',
            'coalesceTo' => -1,
            'join' => [
               'conditions' => [
                  [
                     'field' => 'key',
                     'comparitor' => '=',
                     'value' => 'published',
                  ],
                  [
                     'field' => 'channel_id',
                     'comparitor' => '=',
                     'value' => '54b3d69703e22dd9ecccc786',
                  ],
               ]
            ],
         ],
      ],
   ];
```

warning: The above sort definition is problematic in two ways, which we'll address in the next two sections.

## Level 2.5 Sorting - Casting

One problem with the sort we just defined for published posts is that the numeric user_stats values are actually stored as strings in the database.  That means that in order to perform meaningful sorting, we need to cast the values.  Fortunately, our sorting system is capable of doing this -- we just have to add a single ``cast`` key to our sort definition.

```
   $columns = [
      'posts_published' => [
         'type' => 'number',
         'title' => 'Posts Published',
         'requires' => ['published_posts'],
         'use' => 'published_posts',
         'sortBy' => [
            'table' => 'user_stats',
            'sortField' => 'value',
            'coalesceTo' => -1,
            'cast' => 'int',
            'join' => [
               'conditions' => [
                  [
                     'field' => 'key',
                     'comparitor' => '=',
                     'value' => 'published',
                  ],
                  [
                     'field' => 'channel_id',
                     'comparitor' => '=',
                     'value' => '54b3d69703e22dd9ecccc786',
                  ],
               ]
            ],
         ],
      ],
   ];
```

## Level 3 Sorting - Related Table, Variable Condition

The other problem that you may have noticed with our published posts sort is that sorts are defined in code, at "compile time", but we only know at runtime which channel we're viewing in the manage dash -- and the dash shares a table definition between all channels.  We don't want to sort by just TODAY channel published posts on any and every manage dash.  We need to vary the channel ID based on which dash we're on.

We accomplish this via interpolation.  Variables for interpolation are declared in the format ``{{var_name}}``.  Values for permitted interpolation variables are passed in at runtime to the RequestHelper (see below), which performs the interpolation.

So our **real** published posts sort looks more like this:

```
   $columns = [
      'posts_published' => [
         'type' => 'number',
         'title' => 'Posts Published',
         'requires' => ['published_posts'],
         'use' => 'published_posts',
         'sortBy' => [
            'table' => 'user_stats',
            'sortField' => 'value',
            'coalesceTo' => -1,
            'cast' => 'int',
            'interpolable' => ['join',],
            'join' => [
               'conditions' => [
                  [
                     'field' => 'key',
                     'comparitor' => '=',
                     'value' => 'published',
                  ],
                  [
                     'field' => 'channel_id',
                     'comparitor' => '=',
                     'value' => '{{channel_id}}',
                  ],
               ]
            ],
         ],
      ],
   ];
```

Notice the new ``interpolable`` key as well, which specifies which sort keys will actually be passed through the interpolation routine.

## Level 4 Sorting - Related Table, Aggregate Lookup

Sometimes we're sorting by an aggregate value.  For instance, in newadmin, we show the sum of a user's published post counts across all channels, and the max of total posts associated with the account.  Or we might want to sort users by their greatest following on **any** social network; or their total following across all networks.

To do this, we need to use SQL aggregates.  This is very simple to apply to the sort definition -- for instance, to sort by total social following, we might do:

```
   $columns = [
      'total_social_following' => [
         'type' => 'total_following',
         'title' => 'Total Social Following',
         'sortBy' => [
            'table' => 'user_socials',
            'sortField' => 'followers_count',
            'coalesceTo' => -1,
            'aggregate' => 'SUM',
            ],
         ],
      ],
   ];
```

## Using Sorting in Requests/Controllers



## RequestHelper and QueryHelper

The RequestHelper class, asssisted by the QueryHelper, lets you use request data to apply sorting to a query in just a few lines of code instead of hundreds.  Some of the things that together they do for sorting are:

 * Uses the ``sortBy`` request parameter to look up the column by which the data should be sorted; determine what the fallback sort order should be if none is specified or if the specified column is not eligible for sorting
 * Transform the sort definition to a format that is useful to actually apply the sort
 * Interpolate whitelisted variables into the sort definition
 * Apply joins and grouping needed for sorting


### Usage

Sorting definitions need to be read from Kohana config and passed through when instantiating a RequestHelper instance:

```
   $reqHelper = RequestHelper::factory(
      $request,
      \Tidal\User::class, // The model that we'll be searching for records
      \Gator\Helper\UserHelper::class, // A class that may define custom transformations
                                       // or filters that must be applied to every query.
                                       // If you will not be filtering, this can be NULL
      Kohana::$config->load('filters.users'), // An array of filter definitions; not used for sorting
                                             // If you will not be filtering, you can pass NULL here instead.
      Kohana::$config->load('tables.users'), // An array of column definitions, some of which are eligible for sorting.
      ['channel_id' => (string) $this->channel->_id] // An array of variables that may be interpolated into sort and filter definitions.
   );
   $query = $reqHelper->makeQuery(['users.*']) // create a basic SELECT, specifying fields to include in the SELECT clause
      ->filterQuery()
      ->sortQuery(true) // the boolean argument indicates whether or not grouping needs to be applied
      ->getQuery
      ;
```
