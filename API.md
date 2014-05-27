_Revised May 27, 2014_

# Table of Contents

----------------------------------------

  - [Channel Context](#v1-channel)
    - [Posts](#v1-channel-posts)
      - [Get a post by ID.](#v1-channel-posts-_id)
        - [Reject a post.](#v1-channel-posts-_id-reject)
        - [Unpublish a post.](#v1-channel-posts-_id-unpublish)
        - [Publish a post.](#v1-channel-posts-_id-publish)
        - [Request a post.](#v1-channel-posts-_id-request)
        - [Set content.](#v1-channel-posts-_id-content)
        - [Get a post's references.](#v1-channel-posts-_id-references)
        - [Get a post by slug.](#v1-channel-posts-slug-_slug)
      - [Get published posts.](#v1-channel-posts-published)
        - [Sync all published posts](#v1-channel-posts-published-sync)
      - [Get submitted posts.](#v1-channel-posts-submitted)
      - [Get requested posts.](#v1-channel-posts-requested)
    - [Get users.](#v1-channel-users)
      - [Get a user.](#v1-channel-users-_id)
        - [Get a user's posts.](#v1-channel-users-_id-posts)
        - [Get a user's badges.](#v1-channel-users-_id-badges)
          - [Remove a user's badge.](#v1-channel-users-_id-badges-remove)
          - [Give a user a badge.](#v1-channel-users-_id-badges-add)
        - [Get a user by slug.](#v1-channel-users-slug-_slug)
    - [Products](#v1-channel-products)
      - [Get a product.](#v1-channel-products-_id)
    - [Get this channel's badges.](#v1-channel-badges)
      - [Get a badge.](#v1-channel-badges-_id)
    - [Channel Authentication](#v1-channel-auth)
      - [Get an access token.](#v1-channel-auth-token)
  - [Public Context](#v1-public)
    - [Posts](#v1-public-posts)
      - [Get a post.](#v1-public-posts-_id)
        - [Get a post by slug.](#v1-public-posts-slug-_slug)
      - [Get published posts.](#v1-public-posts-published)
      - [Get a post.](#v1-public-posts-submitted)
      - [Get a post.](#v1-public-posts-requested)
    - [Get users.](#v1-public-users)
      - [Get a user.](#v1-public-users-_id)
    - [Products](#v1-public-products)
      - [Get a product.](#v1-public-products-_id)


----------------------------------------

#  /v1/channel<a name="v1-channel"></a>

**See Also**

 - [/v1/channel/posts](#v1-channel-posts)
 - [/v1/channel/users](#v1-channel-users)
 - [/v1/channel/products](#v1-channel-products)
 - [/v1/channel/badges](#v1-channel-badges)
 - [/v1/channel/auth](#v1-channel-auth)



----------------------------------------

##  /v1/channel/posts<a name="v1-channel-posts"></a>

*Objects that represent articles, blog posts, and long-form content.*

The Post object is the most interesting of Tidal's datatypes. Posts are generally authored by users and enter a publication, submission, and approval workflow. 

**Returns** GET requests generally return either a detailed or summary post object. Post requests typically return an OK or error response.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/_id](#v1-channel-posts-_id)
 - [/v1/channel/posts/slug/_slug](#v1-channel-posts-slug-_slug)
 - [/v1/channel/posts/published](#v1-channel-posts-published)
 - [/v1/channel/posts/published/sync](#v1-channel-posts-published-sync)
 - [/v1/channel/posts/submitted](#v1-channel-posts-submitted)
 - [/v1/channel/posts/requested](#v1-channel-posts-requested)
 - [/v1/channel/posts/_id/reject](#v1-channel-posts-_id-reject)
 - [/v1/channel/posts/_id/unpublish](#v1-channel-posts-_id-unpublish)
 - [/v1/channel/posts/_id/publish](#v1-channel-posts-_id-publish)
 - [/v1/channel/posts/_id/request](#v1-channel-posts-_id-request)
 - [/v1/channel/posts/_id/content](#v1-channel-posts-_id-content)
 - [/v1/channel/posts/_id/references](#v1-channel-posts-_id-references)



----------------------------------------

### GET /v1/channel/posts/_id<a name="v1-channel-posts-_id"></a>

*Return a single post's details by ID.*

Return a single post by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: URL parameter: the post's 24-hexadecimal ID.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/slug/_slug](#v1-channel-posts-slug-_slug)



----------------------------------------

#### POST /v1/channel/posts/_id/reject<a name="v1-channel-posts-_id-reject"></a>

*Rejects a post that has been submitted for approval.*

**Query Parameters**

 - **_id**: Specify the post ID to reject.

**Returns** Success or failure status.

 - **ok**: True or false.

**See Also**

 - [/v1/channel/posts/_id/publish](#v1-channel-posts-_id-publish)
 - [/v1/channel/posts/_id/unpublish](#v1-channel-posts-_id-unpublish)
 - [/v1/channel/posts/_id/request](#v1-channel-posts-_id-request)



----------------------------------------

#### POST /v1/channel/posts/_id/unpublish<a name="v1-channel-posts-_id-unpublish"></a>

*Unpublishes a post that's currently published on the channel.*

**Query Parameters**

 - **_id**: Specify the post ID to unpublish.

**Returns** Success or failure status.

 - **ok**: True or false.

**See Also**

 - [/v1/channel/posts/_id/publish](#v1-channel-posts-_id-publish)
 - [/v1/channel/posts/_id/reject](#v1-channel-posts-_id-reject)
 - [/v1/channel/posts/_id/request](#v1-channel-posts-_id-request)



----------------------------------------

#### POST /v1/channel/posts/_id/publish<a name="v1-channel-posts-_id-publish"></a>

*Publishes a post that's currently awaiting moderation.*

This method will trigger the "postpublish" email template.

**Query Parameters**

 - **_id**: Specify the post ID to publish.

**Returns** Success or failure status.

 - **ok**: True or false.

**See Also**

 - [/v1/channel/posts/_id/unpublish](#v1-channel-posts-_id-unpublish)
 - [/v1/channel/posts/_id/reject](#v1-channel-posts-_id-reject)
 - [/v1/channel/posts/_id/request](#v1-channel-posts-_id-request)



----------------------------------------

#### POST /v1/channel/posts/_id/request<a name="v1-channel-posts-_id-request"></a>

*Request a user's post.*

This method will trigger the "post-request" email template.

**Query Parameters**

 - **_id**: Specify the post ID to request.

**Returns** Success or failure status.

 - **ok**: True or false.

**See Also**

 - [/v1/channel/posts/_id/unpublish](#v1-channel-posts-_id-unpublish)
 - [/v1/channel/posts/_id/reject](#v1-channel-posts-_id-reject)
 - [/v1/channel/posts/_id/publish](#v1-channel-posts-_id-publish)



----------------------------------------

#### POST /v1/channel/posts/_id/content<a name="v1-channel-posts-_id-content"></a>

*Edit a post's content.*

Content is cleaned and sanitized when set. Scripts and CSS are generally moved; iframes are removed except for those on Tidal's whitelist.

**Query Parameters**

 - **_id**: Route parameter. Specify the post ID to request.
 - **content**: The content to set for the post. Can be plaintext or HTML-formatted.

**Returns** Success or failure status.

 - **ok**: True or false.

**See Also**

 - [/v1/channel/posts/_id/unpublish](#v1-channel-posts-_id-unpublish)
 - [/v1/channel/posts/_id/reject](#v1-channel-posts-_id-reject)
 - [/v1/channel/posts/_id/publish](#v1-channel-posts-_id-publish)



----------------------------------------

#### GET /v1/channel/posts/_id/references<a name="v1-channel-posts-_id-references"></a>

*Return a single post's reference details by ID.*

Return a single post's references by its ID.

**Query Parameters**

 - **_id**: URL parameter: the post's 24-hexadecimal ID.
 - **model**: (Optional) the type of reference to filter by.

**Returns** A reference detail object.

**See Also**

 - [/v1/channel/posts/_id](#v1-channel-posts-_id)



----------------------------------------

#### GET /v1/channel/posts/slug/_slug<a name="v1-channel-posts-slug-_slug"></a>

*Return a single post's details by its slug.*

Return a single post by its slug. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_slug**: URL parameter: the post's slug.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/_id](#v1-channel-posts-_id)



----------------------------------------

### GET /v1/channel/posts/published<a name="v1-channel-posts-published"></a>

*Return pages of published post summary objects.*

This method returns a list of post summary objects for published posts only. This method is most useful when building an admin interface that needs to be paginated through by an end user, or, for instance, when building a site that displays the content live (but please cache the data using /channel/posts/published/sync instead!).

Since the pagination for this method accepts page numbers, it's generally not suitable for synchronizing posts to a database on a cron task basis. The /channel/posts/published/sync endpoint is much better suited for that type of task, since it sorts by syncId and allows you to set a minimum syncId as part of the parameters.

**Query Parameters**

 - **page**: Specify a page number. Defaults to 1.
 - **showTotal**: Set to true to show the total number of posts that matched this query. May be slow.

**Returns** An array of post summary objects.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.

**See Also**

 - [/v1/channel/posts/published/sync](#v1-channel-posts-published-sync)
 - [/v1/channel/posts/submitted](#v1-channel-posts-submitted)
 - [/v1/channel/posts/requested](#v1-channel-posts-requested)



----------------------------------------

####  /v1/channel/posts/published/sync<a name="v1-channel-posts-published-sync"></a>



----------------------------------------

### GET /v1/channel/posts/submitted<a name="v1-channel-posts-submitted"></a>

*Return a list of posts currently awaiting approval.*

This method returns a list of post summary objects for posts currently awaiting approval. This method is most useful when building an admin interface that needs to be paginated through by an end user.

**Query Parameters**

 - **page**: Specify a page number. Defaults to 1.
 - **showTotal**: Set to true to show the total number of posts that matched this query. May be slow.

**Returns** An array of post summary objects.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.

**See Also**

 - [/v1/channel/posts/published/sync](#v1-channel-posts-published-sync)
 - [/v1/channel/posts/submitted](#v1-channel-posts-submitted)
 - [/v1/channel/posts/requested](#v1-channel-posts-requested)



----------------------------------------

### GET /v1/channel/posts/requested<a name="v1-channel-posts-requested"></a>

*Return a list of posts currently requested.*

This method returns a list of post summary objects for posts currently requested of a user. This method is most useful when building an admin interface that needs to be paginated through by an end user.

**Query Parameters**

 - **page**: Specify a page number. Defaults to 1.
 - **showTotal**: Set to true to show the total number of posts that matched this query. May be slow.

**Returns** An array of post summary objects.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.

**See Also**

 - [/v1/channel/posts/published/sync](#v1-channel-posts-published-sync)
 - [/v1/channel/posts/submitted](#v1-channel-posts-submitted)
 - [/v1/channel/posts/requested](#v1-channel-posts-requested)



----------------------------------------

## GET /v1/channel/users/<a name="v1-channel-users"></a>

*List users associated with this channel.*

Returns an array of user summary objects. Sorting options may be available in the future.

**Query Parameters**

 - **page**: Query parameter: page number.

**Returns** An array of user summary objects.

 - **_id**: Globally-unique identifier for the user. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the user. Only unique within the users endpoint.
 - **picture**: CDN URL to the user's profile picture. Safe to use in production.
 - **name**: The user's real name. DO NOT USE on the front end where it is publicly visible.
 - **grade**: Profile grade, from A+ to F.
 - **joined**: Date the user joined the channel. ISO 8601.
 - **blog**: Blog summary object.
 - **stats**: Post stats on this channel. Number of published, submitted, featured, and total posts.

**See Also**

 - [/v1/channel/users/_id](#v1-channel-users-_id)
 - [/v1/channel/users/slug/_slug](#v1-channel-users-slug-_slug)
 - [/v1/channel/users/_id/posts](#v1-channel-users-_id-posts)
 - [/v1/channel/users/_id/badges](#v1-channel-users-_id-badges)
 - [/v1/channel/users/_id/badges/remove](#v1-channel-users-_id-badges-remove)
 - [/v1/channel/users/_id/badges/add](#v1-channel-users-_id-badges-add)



----------------------------------------

### GET /v1/channel/users/_id<a name="v1-channel-users-_id"></a>

*Return a single user's details by ID.*

Return a single user by its ID.

**Query Parameters**

 - **_id**: URL parameter: the user's 24-hexadecimal ID.

**Returns** A user detail object.

 - **_id**: Globally-unique identifier for the user. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the user. Only unique within the users endpoint.
 - **picture**: CDN URL to the user's profile picture. Safe to use in production.
 - **name**: The user's real name. DO NOT USE on the front end where it is publicly visible.
 - **grade**: Profile grade, from A+ to F.
 - **joined**: Date the user joined the channel. ISO 8601.
 - **blog**: Blog summary object.
 - **stats**: Post stats on this channel. Number of published, submitted, featured, and total posts.
 - **age**: The user's current age.
 - **email**: The user's email address. DO NOT PUBLICLY DISCLOSE.
 - **profile**: Profile text for the user.
 - **badges**: Array of badge summary objects that the user owns on this channel.
 - **location**: Human-readable user location.

**See Also**

 - [/v1/channel/users/](#v1-channel-users)



----------------------------------------

#### GET /v1/channel/users/_id/posts<a name="v1-channel-users-_id-posts"></a>

*Get posts for a user on this channel.*

Returns an array of post summary objects.

**Query Parameters**

 - **page**: GET parameter
 - **filter**: If omitted, will return all posts. Other values are published, submitted, ever_submitted, and requested.

**Returns** An array of post summary objects.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.

**See Also**

 - [/v1/channel/posts/published](#v1-channel-posts-published)
 - [/v1/channel/users/_id](#v1-channel-users-_id)



----------------------------------------

#### GET /v1/channel/users/_id/badges<a name="v1-channel-users-_id-badges"></a>

*List badge details for a user.*

Returns an array of badge detail objects.

**Returns** An array of badge detail objects.

 - **_id**: Globally-unique identifier for the badge. 24 hexadecimal characters
 - **name**: The badge's name.
 - **image**: A link to the badge image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **slug**: Human-readable identifier for the badge. Only unique within the badges endpoint.
 - **description**: The badge's description.
 - **channelId**: The ID of the channel that owns this badge.

**See Also**

 - [/v1/channel/users/_id/badges/remove](#v1-channel-users-_id-badges-remove)
 - [/v1/channel/users/_id/badges/add](#v1-channel-users-_id-badges-add)



----------------------------------------

##### POST /v1/channel/users/_id/badges/remove<a name="v1-channel-users-_id-badges-remove"></a>

*Removes a badge from a user's account.*

Removes a badge from a user and sends them an optional notification email.

**Query Parameters**

 - **badgeId**: The badge ID to award.
 - **sendEmail**: Defaults true; whether an email notification should be sent to the user.

**Returns** Success or failure.

 - **ok**: True if success.

**See Also**

 - [/v1/channel/users/_id/badges/remove](#v1-channel-users-_id-badges-remove)
 - [/v1/channel/users/_id/badges](#v1-channel-users-_id-badges)



----------------------------------------

##### POST /v1/channel/users/_id/badges/add<a name="v1-channel-users-_id-badges-add"></a>

*Awards a badge to a user.*

Awards a badge to a user and sends them an optional notification email.

**Query Parameters**

 - **badgeId**: The badge ID to award.
 - **sendEmail**: Defaults true; whether an email notification should be sent to the user.

**Returns** Success or failure.

 - **ok**: True if success.

**See Also**

 - [/v1/channel/users/_id/badges/remove](#v1-channel-users-_id-badges-remove)
 - [/v1/channel/users/_id/badges](#v1-channel-users-_id-badges)



----------------------------------------

#### GET /v1/channel/users/slug/_slug<a name="v1-channel-users-slug-_slug"></a>

*Return a single user's details by slug.*

Return a single user by its slug.

**Query Parameters**

 - **slug**: URL parameter: the user's slug.

**Returns** A user detail object.

 - **_id**: Globally-unique identifier for the user. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the user. Only unique within the users endpoint.
 - **picture**: CDN URL to the user's profile picture. Safe to use in production.
 - **name**: The user's real name. DO NOT USE on the front end where it is publicly visible.
 - **grade**: Profile grade, from A+ to F.
 - **joined**: Date the user joined the channel. ISO 8601.
 - **blog**: Blog summary object.
 - **stats**: Post stats on this channel. Number of published, submitted, featured, and total posts.
 - **age**: The user's current age.
 - **email**: The user's email address. DO NOT PUBLICLY DISCLOSE.
 - **profile**: Profile text for the user.
 - **badges**: Array of badge summary objects that the user owns on this channel.
 - **location**: Human-readable user location.

**See Also**

 - [/v1/channel/users/_id](#v1-channel-users-_id)



----------------------------------------

##  /v1/channel/products<a name="v1-channel-products"></a>

**See Also**

 - [/v1/channel/products/_id](#v1-channel-products-_id)



----------------------------------------

### GET /v1/channel/products/_id<a name="v1-channel-products-_id"></a>

*Return a product object's details by ID.*

Return a single product by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: Route parameter: the product's 24-hexadecimal ID.

**Returns** A product detail object.

 - **_id**: Globally-unique identifier for the product. 24 hexadecimal characters
 - **name**: The product's name.
 - **exceprt**: An excerpt of the product's description.
 - **price**: The product's price.
 - **url**: Link to the product page.
 - **imageUrl**: A link to the product image's thumbnail.
 - **description**: The product's description.
 - **created**: ISO-8601 formatted timestamp.
 - **category**: The product's category.



----------------------------------------

## GET /v1/channel/badges/<a name="v1-channel-badges"></a>

*Return summaries of the badges owned by this channel.*

Return a summary of all the badges created and existing on this channel. This method is not paginated.

**Returns** An array of badge summary objects.

 - **_id**: Globally-unique identifier for the badge. 24 hexadecimal characters
 - **name**: The badge's name.
 - **image**: A link to the badge image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.

**See Also**

 - [/v1/channel/badges/_id](#v1-channel-badges-_id)



----------------------------------------

### GET /v1/channel/badges/_id<a name="v1-channel-badges-_id"></a>

*Return a badge object's details by ID.*

Return a single badge by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: Route parameter: the post's 24-hexadecimal ID.

**Returns** A badge detail object.

 - **_id**: Globally-unique identifier for the badge. 24 hexadecimal characters
 - **name**: The badge's name.
 - **image**: A link to the badge image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.

**See Also**

 - [/v1/channel/badges/](#v1-channel-badges)



----------------------------------------

##  /v1/channel/auth<a name="v1-channel-auth"></a>

The channel context requires an access token for an administrator of a channel. 
The access token and channel ID must be provided with every request to the 
channel context endpoints. 

**See Also**

 - [/v1/channel/auth/token](#v1-channel-auth-token)



----------------------------------------

### POST /v1/channel/auth/token<a name="v1-channel-auth-token"></a>

*Get an access token for a channel manager.*

Authenticate a user by exchanging a password for an access token here.

After authenticating, the client must pass the channel ID and access token with 
every request. The client may either include the "access_token" and 
"channel_id" URL parameters, or include the Authentication: Bearer header and 
X-Tidal-Channel-Key header.

**Query Parameters**

 - **client_id**: The name of the client being used to access the API. For instance, "thirdparty".
 - **grant_type**: Just "password".
 - **username**: The authenticating user's email address.
 - **password**: The authenticating user's password.

**Returns** Returns an OAuth token, expiry, refresh token, and channel listing.



----------------------------------------

#  /v1/public<a name="v1-public"></a>

**See Also**

 - [/v1/public/posts](#v1-public-posts)
 - [/v1/public/users](#v1-public-users)
 - [/v1/public/products](#v1-public-products)



----------------------------------------

##  /v1/public/posts<a name="v1-public-posts"></a>

*Objects that represent articles, blog posts, and long-form content.*

The Post object is the most interesting of Tidal's datatypes. Posts are generally authored by users and enter a publication, submission, and approval workflow. 

**Returns** GET requests generally return either a detailed or summary post object. Post requests typically return an OK or error response.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/public/posts/_id](#v1-public-posts-_id)
 - [/v1/public/posts/slug/_slug](#v1-public-posts-slug-_slug)
 - [/v1/public/posts/published](#v1-public-posts-published)
 - [/v1/public/posts/published/sync](#v1-public-posts-published-sync)
 - [/v1/public/posts/submitted](#v1-public-posts-submitted)
 - [/v1/public/posts/requested](#v1-public-posts-requested)
 - [/v1/public/posts/_id/reject](#v1-public-posts-_id-reject)
 - [/v1/public/posts/_id/unpublish](#v1-public-posts-_id-unpublish)
 - [/v1/public/posts/_id/publish](#v1-public-posts-_id-publish)
 - [/v1/public/posts/_id/request](#v1-public-posts-_id-request)



----------------------------------------

### GET /v1/public/posts/_id<a name="v1-public-posts-_id"></a>

*Return a single post's details by ID.*

Return a single post by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: URL parameter: the post's 24-hexadecimal ID.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/slug/_slug](#v1-channel-posts-slug-_slug)



----------------------------------------

#### GET /v1/public/posts/slug/_slug<a name="v1-public-posts-slug-_slug"></a>

*Return a single post's details by its slug.*

Return a single post by its slug. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_slug**: URL parameter: the post's slug.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/_id](#v1-channel-posts-_id)



----------------------------------------

### GET /v1/public/posts/published<a name="v1-public-posts-published"></a>

*Return pages of published post summary objects.*

This method returns a list of post summary objects for published posts only. This method is most useful when building an admin interface that needs to be paginated through by an end user, or, for instance, when building a site that displays the content live (but please cache the data using /channel/posts/published/sync instead!).

Since the pagination for this method accepts page numbers, it's generally not suitable for synchronizing posts to a database on a cron task basis. The /channel/posts/published/sync endpoint is much better suited for that type of task, since it sorts by syncId and allows you to set a minimum syncId as part of the parameters.

**Query Parameters**

 - **page**: Specify a page number. Defaults to 1.
 - **showTotal**: Set to true to show the total number of posts that matched this query. May be slow.

**Returns** An array of post summary objects.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.

**See Also**

 - [/v1/channel/posts/published/sync](#v1-channel-posts-published-sync)
 - [/v1/channel/posts/submitted](#v1-channel-posts-submitted)
 - [/v1/channel/posts/requested](#v1-channel-posts-requested)



----------------------------------------

### GET /v1/public/posts/submitted<a name="v1-public-posts-submitted"></a>

*Return a single post's details by ID.*

Return a single post by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: URL parameter: the post's 24-hexadecimal ID.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/slug/_slug](#v1-channel-posts-slug-_slug)



----------------------------------------

### GET /v1/public/posts/requested<a name="v1-public-posts-requested"></a>

*Return a single post's details by ID.*

Return a single post by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: URL parameter: the post's 24-hexadecimal ID.

**Returns** A post detail object.

 - **_id**: Globally-unique identifier for the post. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the post. Only unique within the posts endpoint.
 - **title**: The title of the post.
 - **excerpt**: A brief excerpt of the post. If one isn't explicitly provided we'll use the first 30 words of the content.
 - **writtenOn**: The time and date this post was authored. ISO-8601 timestamp.
 - **publishedOn**: The time and date this post was published. If the post isn't published this will be null. ISO-8601 timestamp otherwise.
 - **syncId**: An incremental synchronization ID, useful for synchronizing posts to a platform like WordPress. You can use the /channel/posts/published/sync endpoint for this.
 - **status**: The publication status of the post. Will be either published, submitted, requested, rejected, or not submitted.
 - **thumbnail**: A link to the image's thumbnail. Hosted on a CDN so it's safe to use this URL directly.
 - **user**: A User Summary object. See /channel/users for details.
 - **content**: Detail view only. The post's content. This content is considered safe to render on your website. We remove JavaScript from the content and only allow a handful of white-listed iframe sources (like YouTube, Vimeo, etc).
 - **images**: Detail view only. An array of image URLs found in this post. All URLs should be ready-to-use CDN URLs that you can safely hotlink to.
 - **tags**: Detail view only. An array of tags on this post. Tags are added by the user, so there is no pre-defined taxonomy.
 - **originalUrl**: Detail view only. If this post was syndicated from a blog, this field will contain the blog's permalink to this post. If not sourced from a blog, this will be null.

**See Also**

 - [/v1/channel/posts/slug/_slug](#v1-channel-posts-slug-_slug)



----------------------------------------

## GET /v1/public/users/<a name="v1-public-users"></a>

*List users associated with this channel.*

Returns an array of user summary objects. Sorting options may be available in the future.

**Query Parameters**

 - **page**: Query parameter: page number.

**Returns** An array of user summary objects.

 - **_id**: Globally-unique identifier for the user. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the user. Only unique within the users endpoint.
 - **picture**: CDN URL to the user's profile picture. Safe to use in production.
 - **name**: The user's real name. DO NOT USE on the front end where it is publicly visible.
 - **grade**: Profile grade, from A+ to F.
 - **joined**: Date the user joined the channel. ISO 8601.
 - **blog**: Blog summary object.
 - **stats**: Post stats on this channel. Number of published, submitted, featured, and total posts.
 - **age**: The user's current age.
 - **email**: The user's email address. DO NOT PUBLICLY DISCLOSE.
 - **profile**: Profile text for the user.
 - **badges**: Array of badge summary objects that the user owns on this channel.
 - **location**: Human-readable user location.

**See Also**

 - [/v1/public/users/_id](#v1-public-users-_id)



----------------------------------------

### GET /v1/public/users/_id<a name="v1-public-users-_id"></a>

*Return a single user's details by ID.*

Return a single user by its ID.

**Query Parameters**

 - **_id**: URL parameter: the user's 24-hexadecimal ID.

**Returns** A user detail object.

 - **_id**: Globally-unique identifier for the user. 24 hexadecimal characters
 - **slug**: Human-readable identifier for the user. Only unique within the users endpoint.
 - **picture**: CDN URL to the user's profile picture. Safe to use in production.
 - **name**: The user's real name. DO NOT USE on the front end where it is publicly visible.
 - **grade**: Profile grade, from A+ to F.
 - **joined**: Date the user joined the channel. ISO 8601.
 - **blog**: Blog summary object.
 - **stats**: Post stats on this channel. Number of published, submitted, featured, and total posts.
 - **age**: The user's current age.
 - **email**: The user's email address. DO NOT PUBLICLY DISCLOSE.
 - **profile**: Profile text for the user.
 - **badges**: Array of badge summary objects that the user owns on this channel.
 - **location**: Human-readable user location.

**See Also**

 - [/v1/public/users/](#v1-public-users)



----------------------------------------

##  /v1/public/products<a name="v1-public-products"></a>

**See Also**

 - [/v1/public/products/_id](#v1-public-products-_id)



----------------------------------------

### GET /v1/public/products/_id<a name="v1-public-products-_id"></a>

*Return a product object's details by ID.*

Return a single product by its ID. All information returned by this endpoint is safe to use and display on-site.

**Query Parameters**

 - **_id**: Route parameter: the product's 24-hexadecimal ID.

**Returns** A product detail object.

 - **_id**: Globally-unique identifier for the product. 24 hexadecimal characters
 - **name**: The product's name.
 - **exceprt**: An excerpt of the product's description.
 - **price**: The product's price.
 - **url**: Link to the product page.
 - **imageUrl**: A link to the product image's thumbnail.
 - **description**: The product's description.
 - **created**: ISO-8601 formatted timestamp.
 - **category**: The product's category.



----------------------------------------

