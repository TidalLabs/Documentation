Generated on 29 Sep 2015

Parent class / interface declaration for Post Adapters that should be subclassed
in any codebase that uses Posts.  Abstract instead of an interface or trait so that
we can successfully use a `final` method.

The Post Adapter subclass in each codebase should be named `\Tidal\Post\Adapter`.

See /tests/Classes/Post/Adapter.php for a really stupid sample implementation.
 