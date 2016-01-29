Generated on 19 Jan 2016

Tidal\Logger Class
==================

A logging class to be used globally by any Tidal Component. This is built on
Monolog and combines a file output on the local server, "INFO" and higher
logged in elasticsearch, and "WARNING" or higher emailed.

This class makes use of mongolog "channels" for easier reading/parsing of
log files. In our context, a channel represents a component. So there's a
"Generic" channel for all components, or very high level logs, but also an
"Image" channel, a "User" channel, etc.

The great thing about this library is, if you create the correct inheritance
classes, you'll never have to worry about channels when writing code. By
using namespaces and the `use` directive, you can automatically use the
"Image" channel when calling plain ol' `Logger::` from inside the Image class,
"User" channel when in the User class, etc.

The implementation for "Magic Scoping" is kina of annoying, in that the
subclasses are short (1 method of two lines), but still non-trivial in that
you have to define a method that calls its parents rather than just
overriding a variable. See Image/Logger.php for an example. Just copy that file
to every component you write.

Usage
-----

Usage is simple. At the top of any file, write

    use \Tidal\Logger;

Or, if you've set up a logger subclass with a custom logging channel (which
is the recommended approach), invoke the subclass that you've created:

    use \Tidal\Image\Logger; // "Image" is an example

(Where 'Image' is just an example). And then write logs like so:

    Logger::debug("Debug message here.");
    Logger::error("An error occurred!", $variable);
    Logger::alert("Wake someone up!!!", ["foo" => $bar, "steven" => $woods]);

If you call the root `\Tidal\Logger` class, the channel will be set to 'Generic'.

If you have a special use-case where you need to write to two different
channels in one place, use the `init()` method to re-init the library. This
is a cheap operation so feel free.

    Logger::init('One Scope');
    Logger::info("This will be in One Scope");
    Logger::init('Two Scope');
    Logger::info("This will be in Two Scope");

Subclasses (Magic Scoping)
--------------------------

To pull off the "Magic" scoping that lets you just call `Log::` and have the
channel handled for you automatically, you need to set up a subclass for
each channel you want automatically scoped. (You can also just totally skip
magic scoping and call `Log::init($channel)`). The subclass is very easy but
slightly non-trivial. Here's an example:

    namespace Tidal\Image;
    class Logger extends \Tidal\Logger
    {
        public static function __callStatic($name, $arguments)
        {
            parent::logToChannel('Image', $name, $arguments);
        }
    }

And that's all you have to do for magic scoping.

Log Levels
----------

Try to use the most appropriate log level for the situation. It's semantics,
but sticking to a system will make all of our lives easier. Here are the
definitions. All of the are loggable just by calling the log level as a
function

DEBUG	Detailed debug information

INFO		Interesting events (user logs in, etc)

NOTICE	Uncommon events

WARNING	Exceptional occurrences that aren't errors (use of deprecated API, etc)

ERROR	Runtime Errors

CRITICAL	Unexpected exception, app component unavailable

ALERT	Action immediately! Site down, DB unavailable, SMS ALERT triggered

EMERGENCY	Very urgent


 