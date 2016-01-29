Generated on 19 Jan 2016

Tidal\Image Class
=================

Manages images. Accepts uploads; re-hosts on Amazon S3; resizes to save
bandwidth; manages the database records for the images as well.

This library also manages a queue worker that processes the images.

Make sure to check out Image\Helper, a helper class with a few convenience
methods for working with images and text.

Basic Usage
-----------

    // Upload an image
    $uploadedImage = \Tidal\Image::upload($_FILES['img']);
    
    // Get the URL for a size.
    // All of the following will return a size
    $imageUrl = $image->getUrl('m');
    $imageUrl = $image->getUrl(600);
    $imageUrl = $image->getUrl('600pxw');
    $imageUrl = \Tidal\Image::url($image, '600pxw', $useDefault, $default);
    $imageUrl = (string) $image->getSize('m')
    
    // Create an image from a URL
    $remoteImage = \Tidal\Image::createFromUrl('http://...');
    
    // Get an image by ID
    $image = \Tidal\Image::find($id);
    
    // Get an image by mongo_id
    $image = \Tidal\Image::where('mongo_id', (string) $mongoId)->first();


Gator Compatibility
-------------------

Since migrating all 17M image records from Gator at once is crazy, this
library lazy-migrates them. In the gator codebase, instead of using the
`\Tidal\Image::where` syntax above, use the `convert()` method instead. This
method understands Mongo IDs and will intelligently migrate the record.

    $image = \Tidal\Image::convert($mongoId);

Image Sizes
-----------

Possible sizes for images are:
- "xs" 100 pixels wide
- "s" 320 pixels wide
- "m" 600 pixels wide
- "l" 1000 pixels wide
- "o" the original Image


Properties
----------

| Name | Type | Description |
|------|------|-------------|
| original_url | string |  |
| processed | bool |  |
 