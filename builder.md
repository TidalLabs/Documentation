# Builder

We are trying to get away from writing HTML in dashboard pages.  Obviously it will be 
needed some of the time for custom behavior, but common rendering should be done with 
builder components where possible.

## Page
The container for all presented elements can be a `/builder/page/basic` component. 
There's a simple example on [Dashboard Overview](./dashboard/overview.md) 

Generally the left column of the page will be the primary data or controls being presented 
and the optional right hand sidebar will contain widgets that filter, search or act on the 
left hand data.

## Widget
We overload the term widget.  In the context of builder components, `/builder/widget` is a 
styled div that wraps both the whole page and wraps the sections within the page.

We also use it to refer to the reusable sections of UI that appear in the right hand column on a 
page. There's a simple example of a widget on [Dashboard Overview](./dashboard/overview.md) 
Like our other page sections, these widgets are wrapped in a `/builder/widget`.

## Form
Try to avoid hand entering HTML to render html form elements.  Within the `/builder/form`
heirarchy, there are fragments that will produce consistently styled elements.  

Realistic code to generate a form might look like this (inside a widget, inside a column of a 
page)
```
View::factory('/builder/form')
    ->set([
        'method' => 'POST',
        'action' => '/newadmin/api/collection',
        'class' => 'api-action-then-reload',
        'fields' => [
            [
                'type' => 'select',
                'name' => 'channelId',
                'label' => '<strong>Channel</strong>',
                'default' => 'Select a Channel',
                'required' => true,
                // We've prepared the $channelSelect above.
                'options' => $channelSelect
            ],
            [
                'type' => 'text',
                'name' => 'name',
                'placeholder' => 'eg: Campaign Candidates',
                'label' => '<strong>List Name</strong>',
                'required' => true
            ],
            [
                'type' => 'submit',
                'text' => 'Create List',
                'class' => 'btn btn-primary'
            ]

        ]
    ])
```

That code is using a `builder\form\text`, a `builder\form\submit`, and a `builder\form\submit`. 

## Table

The most advanced builder functionality renders the paginated, sortable tables that presents most
of our data from a collection of row data and a column specification that determines what 
attributes of each item to present and how to transform the raw data for presentation.

A collection would normally be fetched on the plugin page with code like

```
$lists = Collection::orderBy('channel_id', 'asc')->get();
```
Then passed to a view that uses a `\builder\table` component to display it.

The code to produce the table will look like

```
View::factory('/builder/table/basic')
    ->set([
       'columns' => $columns,
       'collection' => $lists
    ])
```

For simple data, the column specification might be a simple array of mappings between object attribute names 
and the headings for display in the column but for more complex data, most of the work 
is in writing the column specification. Each column can have a human readable 
title for display, a type which might be generic like 'text' and 'number' or very specific like 'minipic'

A realistic column specification might look like this
```
$columns = [
    [
        'title' => 'Channel',
        'type' => 'text',
        'requires' => [
            'channel_name' => function($coll) {
                $cacheKey = 'newadminListBuilderChannelNameById'.$coll->channel_id;
                $cacheLife = 10;
                return \Tidal\Cache::remember($cacheKey, $cacheLife, function() use ($coll) {
                    $channel = \Mango::factory('mangochannel')->load_by_id($coll->channel_id);
                    return $channel->name;
                });
            }
        ],
        'use' => 'channel_name'
    ],
    [
        'title' => 'List',
        'type' => 'text',
        'requires' => [
            'link_to_list' => function($coll) {
                $filters = json_encode([['lists-user_collection_id', '=', $coll->id]]);
                $query = http_build_query([
                    'filters' => $filters,
                    'ctx' => 'list:'.$coll->id
                ]);
                return "<a href=\"/newadmin/do/page/users/all?$query\">{$coll->name}</a>";

            }
        ],
        'use' => 'link_to_list'
    ],
    [
        'title' => '# Users',
        'type' => 'number',
        'requires' => [
            'num_users' => function($coll) {
                $count = $coll->users()->distinct()->count();
                return $count;
            }
        ],
        'use' => 'num_users'

    ]

];
```