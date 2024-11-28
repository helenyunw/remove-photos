## Unofficial API for remove.photos

This is an unofficial API for remove.photos. It is not affiliated with remove.photos in any way.

* [Register an account and obtain your api key](https://remove.photos)
* [remove.photos Developer Documentation](https://remove.photos)

## Installation

Install via composer (Not published to packagist yet):

```
composer require remove-photos
```

*This package is designed to work with any PHP 7.0+ application but has special support for Laravel.*

### Registering the service provider (Laravel users)

For Laravel 5.4 and lower, add the following line to your ``config/app.php``:

```php
/*
 * Package Service Providers...
 */
RemovePhotos\Providers\RemovePhotosServiceProvider::class,
```

For Laravel 5.5 and greater, the package will auto register the provider for you.

### Using Lumen

To register the service provider, add the following line to ``app/bootstrap/app.php``:

```php
$app->register(RemovePhotos\Providers\RemovePhotosServiceProvider::class);
```

### Publishing the config file (Laravel users)

````
php artisan vendor:publish --provider="RemovePhotos\Providers\RemovePhotosServiceProvider"
````

Once your ``RemovePhotos.php`` has been published your to your config folder, add the api key you obtained from [remove.photos](https://remove.photos/). If you are using Laravel and put your remove.photos api key in the config file, Laravel will automatically set your api key every time you instantiate the class through the helper or facade.

## Quick start

### Using the class

```php
use RemovePhotos;

$absoluteUrl = 'https://yoursite.com/images/photo.jpg';
$pathToFile = 'images/avatar.jpg';
$base64EncodedFile = base64_encode(file_get_contents($pathToFile));

$removephotos = new RemovePhotos($apiKey);

// Directly saving files
$removephotos->url($absoluteUrl)->save('path/to/your/file.png');
$removephotos->file($pathToFile)->save('path/to/your/file2.png');
$removephotos->base64($base64EncodedFile)->save('path/to/your/file3.png');

// Getting the file's raw contents to save or do something else with
$rawUrl = $removephotos->url($absoluteUrl)->get();
$rawFile = $removephotos->file($pathToFile)->get();
$rawBase64 = $removephotos->base64($base64EncodedFile)->get();

file_put_contents('path/to/your/file4.png', $rawUrl);
// etc...

// Getting the file's base64 encoded contents from the api
$base64Url = $removephotos->url($absoluteUrl)->getBase64();
$base64File = $removephotos->file($pathToFile)->getBase64();
$base64Base64 = $removephotos->base64($base64EncodedFile)->getBase64();

file_put_contents('path/to/your/file5.png', base64_decode($base64Url));
// etc...

// Please note: remove.photos returns all images in .png format, so you should be saving all files received from the api as .png.
```

### Advanced usage

remove.photos offers several request body parameters for each api call. For an up to date list, you should always check the [remove.photos api documentation](https://remove.photos).

Here is an example of an api call configured with specific request body parameters.

````php
$removephotos = new RemovePhotos($apiKey);

// Directly saving files
$removephotos->url($absoluteUrl)
->body([
    'size' => '4k', // regular, medium, hd, 4k, auto
    'bg_color' => '#CBD5E0',
    'add_shadow' => true, // primarily used for automotive photos as of the time this documentation was written
    'channels' => 'rgba', // rgba, alpha
])
->save('path/to/your/file.png');
````

You may also directly specify request header parameters. As of right now this does not appear to offer much functionality in terms of how the remove.photos api will consume these headers, but we thought it was important to expose this functionality. Consider the following example:

````php
$removephotos = new RemovePhotos($apiKey);

// Directly saving files
$removephotos->url($absoluteUrl)
->headers([
    'X-Foo-Header' => 'Some Bar Value',
    'X-Foo-Header-2' => 'Some Bar Value 2',
])
->save('path/to/your/file.png');
````

### Account details

The remove.photos api offers an endpoint to check your account's credit balance and free api call usage. If your application needs to check your available credits before processing images this package makes it an absolute breeze!

The following code example is how you can programmatically check your account information. Note, the ``account`` method has one optional argument: `$getResponseAsObject = true`. By default your response will be returned as an object. You can return the response as an associative array by passing `false` to the `account(false)` method.

````php
$removephotos = new RemovePhotos($apiKey);

$account = $removephotos->account();

// $account will be something like this:
{
  "data": {
    "attributes": {
      "credits": {
        "total": 200,
        "subscription": 150,
        "payg": 50
      },
      "api": {
        "free_calls": 50,
        "sizes": "all"
      }
    }
  }
}
````

To access your total credits you could do so like this: `$account->data->attributes->credits->total`.

A practical example could look something like the following:

````php
$removephotos = new RemovePhotos($apiKey);

$account = $removephotos->account();

if ($account->data->attributes->credits->total >= 1) {
	$removephotos->url($absoluteUrl)->save('path/to/your/file.png');
}
````

### Using the global helper (Laravel users)

If you are using Laravel, this package provides a convenient helper function which is globally accessible.

```php
RemovePhotos()->url($absoluteUrl)->save(public_path('path/to/your/file.png'));
```

### Using the facade (Laravel users)

If you are using Laravel, this package provides a facade. To register the facade add the following line to your ``config/app.php`` under the ``aliases`` key.

````php
'removephotos' => RemovePhotos\Facades\RemovePhotos::class,
````

```php
use RemovePhotos;

RemovePhotos::file($pathToFile)->save(public_path('path/to/your/file.png'));
```

## Credits

- Mark Townsend
- [All Contributors](../../contributors)

## Testing

*Tests coming soon...*

You can run the tests with:

```bash
./vendor/bin/phpunit
```

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.