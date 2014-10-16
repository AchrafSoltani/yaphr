#tomkyle\yaphr

[![Build Status](https://travis-ci.org/tomkyle/yaphr.svg?branch=master)](https://travis-ci.org/tomkyle/yaphr)

Yet Another Photo Resizer for JPG, PNG, GIF.  
Resizing and cropping, crisp & useful sharpening.


##Installation

Install via Composer; add this to `composer.json`:

```
"require": {
	"tomkyle/yaphr": "~1.0"
}
```

##Dependencies
None, but installing [Slim Framework](https://github.com/codeguy/Slim) is suggested: Yaphr brings it own Slim Middleware for the most common resizing use cases.


##Example Workflow
This example shows how to crop a JPG and save it into a cache directory:

```php
<?php
use \tomkyle\yaphr\ImageFactory;
use \tomkyle\yaphr\Geometry\BoxFactory;
use \tomkyle\yaphr\Resize;
use \tomkyle\yaphr\Filters\SharpenImage;
use \tomkyle\yaphr\FileSystem\CreateCacheDir;
use \tomkyle\yaphr\FileSystem\SaveImage;
use \tomkyle\yaphr\FileSystem\DeliverImage;
use \SplFileInfo;

// YAPHR likes `SplFileInfo`
$source = new SplFileInfo( '../master/path/to/original.jpg' );
$output = new SplFileInfo( './path/to/resized.jpg' );

// Generate Factories:
$image_factory = new ImageFactory;
$box_factory   = new BoxFactory;

// Grab image from JPG/PNG/GIF:
$image   = $image_factory->newInstance( $source );

// Setup resize box:
$crop_box = $box_factory->newInstance( 300, 200, $image, 'crop');

// Create resized (cropped) image:
$resized = new Resize($image, $crop_box);

// Make it nice and crisp:
new SharpenImage( $resized );

// Save to file cache:
new CreateCacheDir( $output, 0775 );
new SaveImage( $resized, $output, 85 );

// Send to client:
new DeliverImage( $output, "exit" );
```


##Resize boxes
Yaphr offers various resizing modes, all of them useful in different use cases.

If you exactly know what you want, you may instantiate a concrete Box class; using the `BoxFactory` with string parameter gives more flexibility: Just pass desired `$width` and `$height`, your original `$image` and the box type.

**Crop** extracts as much as possible from the original that fits into the given width and height. Most useful for pictures with varying side ratios, e.g. in responsive context.
```php
$exact = new CropBox( $width, $height, $image );
// same as
$exact = $box_factory->newInstance( $width, $height, $image, 'crop');
```

**Auto** makes portrait images `$height` pixels high, and landscape ones `$width` pixels, preserving side ratios. Perhaps the most classic resing mode.
```php
$exact = new AutoBox( $width, $height, $image );
// same as
$exact = $box_factory->newInstance( $width, $height, $image, 'auto');
```

**Exact** fits the image in the given width and height, not preserving side ratio (i.e. the result may be distorted). Mostly not useful (except from, well, distorting).
```php
$exact = new Box( $width, $height );
// same as
$exact = $box_factory->newInstance( $width, $height, $image, 'exact');
```

**Tall** resizes to the given height, regardless of the image width, but preserving side ratios. Useful for horizontal “same height” galleries or *Masonry galleries*.
```php
$exact = new TallBox( $height, $image );
// same as
$exact = $box_factory->newInstance( $height, $image, 'tall');
```

**Wide** resizes to the given width, regardless of the image height, but preserving side ratios. Useful for vertical “same width” galleries.
```php
$exact = new WideBox( $width, $image );
// same as
$exact = $box_factory->newInstance( $width, $image, 'wide');
```




##Classes overview

###Image classes

```php
<?php
# Classes
use \tomkyle\yaphr\GifImage;
use \tomkyle\yaphr\JpegImage;
use \tomkyle\yaphr\PngImage;

# Abstracts and Interfaces
use \tomkyle\yaphr\ImageAbstract;
use \tomkyle\yaphr\ImageInterface;
use \tomkyle\yaphr\GifImageInterface;
use \tomkyle\yaphr\JpegImageInterface;
use \tomkyle\yaphr\PngImageInterface;
```

###Business classes

```php
<?php
# Classes
use \tomkyle\yaphr\ImageFactory;
use \tomkyle\yaphr\Workflow;
use \tomkyle\yaphr\Resize;
```



###Geometry classes

```php
<?php
# Boxes
use \tomkyle\yaphr\Geometry\Box;
use \tomkyle\yaphr\Geometry\BoxFactory;
use \tomkyle\yaphr\Geometry\AutoBox;
use \tomkyle\yaphr\Geometry\CropBox;
use \tomkyle\yaphr\Geometry\SquareBox;
use \tomkyle\yaphr\Geometry\TallBox;
use \tomkyle\yaphr\Geometry\WideBox;

# Coordinates
use \tomkyle\yaphr\Geometry\CropStartCoordinates;
use \tomkyle\yaphr\Geometry\NullCoordinates;

# Abstracts and Interfaces
use \tomkyle\yaphr\Geometry\CoordinatesInterface;
use \tomkyle\yaphr\Geometry\BoxAbstract;
use \tomkyle\yaphr\Geometry\BoxInterface;
use \tomkyle\yaphr\Geometry\CropBoxInterface;
```

###Image filter classes
```php
<?php
# Classes
use \tomkyle\yaphr\Filters\SharpenImage;
use \tomkyle\yaphr\Filters\UnsharpMask;
```


###File system classes

```php
<?php
# Classes
use \tomkyle\yaphr\FileSystem\CreateCacheDir;
use \tomkyle\yaphr\FileSystem\CheckReadableFile;
use \tomkyle\yaphr\FileSystem\DeliverImage;
use \tomkyle\yaphr\FileSystem\FileExtension;
use \tomkyle\yaphr\FileSystem\SaveImage;
use \tomkyle\yaphr\FileSystem\SaveGif;
use \tomkyle\yaphr\FileSystem\SaveJpeg;
use \tomkyle\yaphr\FileSystem\SavePng;

# Abstracts and Interfaces
use \tomkyle\yaphr\FileSystem\SaveImageAbstract;
use \tomkyle\yaphr\FileSystem\SaveImageInterface;
```

###Exceptions

```php
<?php
# Classes and Interfaces
use \tomkyle\yaphr\Exceptions\FileNotFound;
use \tomkyle\yaphr\Exceptions\YaphrException;
use \tomkyle\yaphr\Exceptions\YaphrExceptionInterface;
```

###PHP resource aggregation

```php
<?php
# Interfaces and traits
use \tomkyle\yaphr\Resources\ResourceAggregateuse \tomkyle\yaphr\Resources\ResourceAggregateTrait
```


