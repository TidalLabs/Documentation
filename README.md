Tidal Labs API Documentation
============================

The Tidal API allows you to interact with contributed content, campaign
workflows, contributors themselves, and all of our auxiliary tools that power
delivery and interaction. 




## Before you start
-------------------

### Channels

Before you start, you'll need to be a Tidal customer and receive a _channel
key_. A _channel_ is is the primary type of customer account with Tidal. An
individual user or company can manage multiple channels, and users can belong
to multiple channels, but a channel always represents a single campaign, family
of campaigns, or community. 

### Testing the API

There are a couple of tools available to make test requests to the API. If
you're comfortable with the command line, we recommend curl. As you can see,
our [authentication](#authentication) is simple enough to not be a nuisance for
command line testing. 

If, however, you prefer more user friendly tools, we recommend 
[Postman](http://www.getpostman.com/). 




## Core Concepts
----------------

### Contexts

The Tidal API has three contexts, each with a slightly different authentication
scheme.

#### The Channel Context

The channel context (also called the "management" context) uses endpoints
starting in /v1/channel. This context gives you access to management tools.
Through this context you can evaluate contributors, moderate content, edit
content, move through workflows, award badges, and do anything that you could
do through our admin panel.

Authentication to the channel contexts requires both an access token and your
channel key. See [authentication](#authentication) for details.

#### The Public Context

The public context exposes information that should be publicly viewable. Our
JavaScript widgets and frontend implementations use the public context to get
content and user profiles to display. Everything in the public endpoints is
safe to display on site, meaning that all posts have been approved and
published, and no private information (email addresses, personal names, zip
codes, usage statistics, etc) is divulged.

Since the public context only serves public information, and is often used by a
JavaScript frontend, the only authentication required is your public channel
key. See [authentication](#authentication) for details.

#### The "Me" Context

The "me" context, also called the "user" context, represents the actions of an
invidual user. This context allows a user to edit their own content and
participate in workflows and interactions from their own perspective. It is
rare you'll use this context unless you wish to build an entire user dashboard
on top of the Tidal framework. 

Since this context can be used to build 3rd party dashboards, it requires
OAuth application authorization first, and then common usage requires an access
token and a channel key for channel-specific actions.




## Authentication
-----------------

The Tidal API uses OAuth 2.0. The exact implementation and grant type depend on
the [context](#contexts).

### Use Headers Rather Than Query Parameters

Whenever possible, authentication parameters should be included as headers
rather than query parameters, though we do support both. 

### Channel Key

Most contexts require a 24 character hexadecimal _channel key_. This should be
provided as the `X-Tidal-Channel-Key` header. If it is not possible to use a
header, provide it as the `channelId` query parameter.

### Access Token

Management and user contexts require an access token. Provide this as the
`Authorization` token. For example:

```
Authorization: Bearer <Your Token>
```

### Obtaining an Access Token

The *channel/management* context provides a [resource owner password
credentials](http://tools.ietf.org/html/rfc6749#section-4.3) grant type
interface at `/v1/channel/auth/token`. POST the following parameters to this
endpoint:

Parameter 	| Value 					| Description
----------------------------------------------------------------------------------------------------------------------------
grant_type	| "password"					| Indicates ROPC grant.
client_id	| "thirdparty"					| Indicates that you're a third-party client.
username	| Email address of the authenticating admin.	| Note that the user must have admin rights to this channel.
password	| Password of the authenticating admin.		| The admin's password.
----------------------------------------------------------------------------------------------------------------------------

The *user* context implements an [authorization code
grant](http://tools.ietf.org/html/rfc6749#section-4.1). Details TBD.

Once you've obtained your access token, include it with requests as part of the
`Authorization` header along with the channel key in the `X-Tidal-Channel-Key`
header.



## API Responses
----------------

This API is fully JSON -- there are no plans to ever support XML or any other
formats.

### OPTIONS Endpoints

The Tidal API is self-documenting; you can make an OPTIONS request to any
endpoint to see relevant documentation, query parameters, and response formats
for any endpoint.

In fact, the rest of this API documentation was built using the information
returned by the OPTIONS endpoints.

### Meta and Data Fields

All API responses (with the rare exception of a disastrous error) include both
a `meta` and a `data` field in the response body. The meta field includes data
like the count of results returned, the total count of all possible results,
the time taken to process the request, the HTTP status code, and the error
message if applicable. 

The `data` field can return different data types depending on the endpoint
called, but is typically either a single object or a one-dimensional array of
objects.
