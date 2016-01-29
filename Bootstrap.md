Generated on 19 Jan 2016

Tidal\Bootstrap Class
=====================

Every application that uses Tidal Components must invoke the Bootstrap
class. The Bootstrap class is responsible for loading configuration and
setting up the environment (database handles, logging, etc) for use. The
Bootstrap class, not trying to be a fancy dependency injection or IOC
container, just manages these resources using a singleton pattern. We can
change that in the future if it ever becomes a problem.

Usage
-----

To use Tidal Components in your app, just call:

    \Tidal\Bootstrap::setEnvironment($environment);
    \Tidal\Bootstrap::init();

Where `$environment` is one of "testing", "development", "production", OR
any other environment string you decide to add to the config ("staging" and
"local" could be good candidates).  If you omit setEnvironment, it will try to
detect it from the TIDAL_ENV variable.  If TIDAL_ENV is not found, it will default
to "development"

Make SURE to check that the `$config` below is correct.

If you need to customize configuration parameters, just override them _before_ calling `init()` statically:

    \Tidal\Bootstrap::setEnvironment('testing');
    \Tidal\Bootstrap::config()->paypal->clientId = 'myClientId';
    \Tidal\Bootstrap::init();

Mongo DB Handles
----------------

Since Mongo SSL doesn't seem to like having more than one connection to the
server open (why? Ask Burak why "two of the same connection strings causes a
segfault?!"), the Bootstrap class manages a MongoDB singleton that you can
access at `Bootstrap::mongo()`.

If your app connects to the same mongo that Bootstrap does, (in the case of
Gator), you _need_ to manually set the mongo handle to use the one your app
is using. That way the system only uses one mongo handle.

An example:

    \Tidal\Bootstrap::$mongo = $myMongoClient;
    \Tidal\Bootstrap::init();

Where `$myMongoClient` is a valid `MongoClient` instance. If you don't do
this on an app that uses mongo you might get segfaults, for some reason.

 