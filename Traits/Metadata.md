Generated on 19 Jan 2016

Traits\Metadata Class
=====================

Add metadata handling methods to any model that has a metadata field.

This trait defines a simple interface around managing basic JSON-based 
metadata and key/value lookups. Right now there is no support for advanced 
features like dot notation. This is a very simple interface that gives you 
the `getMetadata` and `setMetadata` methods, and handles the JSON encoding 
for you.

Use this on classes that need relatively unstructured and unpredictable 
metadata added to them by an application, _and_ don't have a requirement for 
querying records based on metadata. You _can_ query JSON but it's slow at 
our scale.

Usage
-----

Add the Metadata trait to your class:

    class Something {
        use Traits\Metadata;
    }

Set a metadata value:

    $record = new Something;
    $record->setMetadata("key", "value");
    $record->setMetadata("aNumber", 123);
    $record->setMetadata("it's just an array so any valid key works", true);
    $record->setMetadata("array", ["it's", "also", "json", "so use any valid JSON value");
    // You still need to SAVE the record!!
    $record->save();
    // Or update()

Getting a metadata value:

    $record->getMetadata("key"); // Returns null if doesn't exist.

 