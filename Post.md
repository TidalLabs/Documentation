Generated on 29 Sep 2015

\Tidal\Post Model
=================

For now, this model is barebones and its Postgres record contains nothing but a MongoId reference.
Its purpose is to make it possible to reference posts from Components in a relational
manner.

Next steps will include an adapter for each of Gator and API to support some basic moderation
functionality (such as publish, unpublish, campaign approval and rejection) to support 
Campaigns integration.

Currently, all data from Mongo can be accessed as an array, but property access with 
``$post->some_attr`` syntax is intended Real Soon Now.

Basic Usage
-----------

     // get Eloquent representation of a Post. 
     // Will automatically copy from Mongo if post exists in Mongo but not Postgres yet.
     $post = \Tidal\Post::findByMongoId($mongo_id_string);
     if ($post) {
         $some_var = $post->some_mongo_only_var;
     }

 