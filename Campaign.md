Generated on 19 Jan 2016

Tidal\Campaign Class
=================

The Campaign stores relatively permanent information about the campaign such as
name, start and end dates, image and the owning channel.

Basic Usage
-----------

    // Get a campaign by ID
    $campaign = \Tidal\Campaign::find($id);

    // Get a campaign by mongo_id
    $campaign = \Tidal\Campaign::where('mongo_id', (string) $mongoId)->first();


Properties
----------

| Name | Type | Description |
|------|------|-------------|
| workflow | \Tidal\Campaign\Workflow |  |
| channel_id | string | MongoId expressed as a string |
| slug | string |  |
| description | string |  |
| image_id | string |  |
| end | \Carbon\Carbon |  |
| start | \Carbon\Carbon |  |
| created_at | \Carbon\Carbon |  |
| updated_at | \Carbon\Carbon |  |
| owner_id | int |  |
| tags | array |  |
| name | string |  |
| id | int |  |
| settings | mixed |  |
 
Methods
-------

### public __set(key, value)

custom setter so we can handle settings and let actual members fall through to default handling

     
### public countUsers()

How many users are in workflows in this campaign
return array of started, active, completed

started = how many users entered this workflow
active = how many users are currently in this workflow
completed = how many users reached completion

If we have an eject method started may not be the sum of completed and active

Note not yet implemented, which seems like a problem

     
### public setSettingDefinition(name, type, required, label, [description], [placeholder])

campaign settings are a little odd.
They store their definition in their workflow, not directly in the campaign
     
### public report([user])

Get JSON formatted summary reporting data on campaign
(posts (submitted, rejected, pending, published), contributors, shares (by network),
pageviews, sphere of influence, top posts (date, title, url, votes, views, shares),
reach (blog, twitter)

Supports replacing current display at /manage/do/page/stats/campaigns but can use
published posts rather than all submitted posts

Note, not yet implemented.  Perhaps should be deleted

     