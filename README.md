
<h3 align="center"><img src="https://user-images.githubusercontent.com/803299/42567830-6b6d3ad6-850b-11e8-9151-43021c92d8b7.png"></h3>
# I want to use this repo from https://github.com/ckfinder/ckfinder-laravel-package
But it got a trouble with Laravel 6.5 
Monolog package new version 
# CKFinder 3 Package for Laravel 5.5+ [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Check%20out%20CKFinder%20package%20for%20Laravel%20&url=https://github.com/ckfinder/ckfinder-laravel-package)

[![Laravel version](https://img.shields.io/badge/Laravel-5.5%2C%205.6-green.svg)]()
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Packagist](https://img.shields.io/packagist/v/ckfinder/ckfinder-laravel-package.svg)](https://packagist.org/packages/ckfinder/ckfinder-laravel-package)
[![Packagist](https://img.shields.io/packagist/dt/ckfinder/ckfinder-laravel-package.svg)](https://packagist.org/packages/ckfinder/ckfinder-laravel-package)
[![Join newsletter](https://img.shields.io/badge/join-newsletter-00cc99.svg)](http://eepurl.com/c3zRPr)
[![Follow twitter](https://img.shields.io/badge/follow-twitter-00cc99.svg)](https://twitter.com/ckeditor)

This repository contains the CKFinder 3 Package for Laravel 5.5+.

<h3 align="center"><a href="https://ckeditor.com/docs/ckfinder/demo/ckfinder3/samples/widget.html"><img src="https://user-images.githubusercontent.com/803299/42693315-18717aae-86af-11e8-863a-74070edb3912.png"></a></h3>

## Installation

1. Add a Composer dependency and install the package.

    ```bash
    composer require ckfinder/ckfinder-laravel-package
    ```

2. Run the command to download the CKFinder code.

    After installing the Laravel package you need to download CKFinder code. It is not shipped with the package due to different license terms. To install it, run the following `artisan` command:

    ```bash
    php artisan ckfinder:download
    ```

    It will download the required code and place it inside an appropriate directory of the package (`vendor/ckfinder/ckfinder-laravel-package/`).

3. Publish the CKFinder connector configuration and assets.

    ```bash
    php artisan vendor:publish --tag=ckfinder
    ```

    This will publish CKFinder assets to `public/js/ckfinder`, and the CKFinder connector configuration to `config/ckfinder.php`.

4. Create a directory for CKFinder files and allow for write access to it. By default CKFinder expects the files to be placed in `public/userfiles` (this can be altered in the configuration).

    ```bash
    mkdir -m 777 public/userfiles
    ```

**NOTE:** Since usually setting permissions to `0777` is insecure, it is advisable to change the group ownership of the directory to the same user as Apache and add group write permissions instead. Please contact your system administrator in case of any doubts.

At this point you should see the connector JSON response after navigating to the `<APP BASE URL>/ckfinder/connector?command=Init` address.
Authentication for CKFinder is not configured yet, so you will see an error response saying that CKFinder is not enabled.

## Configuring Authentication

CKFinder connector authentication is handled by [middleware](https://laravel.com/docs/5.8/middleware) class or alias. To create the custom middleware class, use the artisan command:

```bash
php artisan make:middleware CustomCKFinderAuth
```

The new middleware class will appear in `app/Http/Middleware/CustomCKFinderAuth.php`. Change the `authentication` option in `config/ckfinder.php`:

```php
$config['authentication'] = '\App\Http\Middleware\CustomCKFinderAuth';
```

The `handle` method in `CustomCKFinderAuth` class allows to authenticate CKFinder users. A basic implementation that returns `true` from the `authentication` callable (which is obviously **not secure**) can look like below:

```php
public function handle($request, Closure $next)
{
    config(['ckfinder.authentication' => function() {
        return true;
    }]);
    return $next($request);
}
```

Please have a look at the [CKFinder for PHP connector documentation](https://ckeditor.com/docs/ckfinder/ckfinder3-php/configuration.html#configuration_options_authentication) to find out
more about this option.



## Configuration Options

The CKFinder connector configuration is taken from the `config/ckfinder.php` file.

To find out more about possible connector configuration options please refer to the [CKFinder for PHP connector documentation](https://ckeditor.com/docs/ckfinder/ckfinder3-php/configuration.html).

## Usage

The package code contains a couple of usage examples that you may find useful. To enable them, uncomment the `ckfinder_examples`
route in `vendor/ckfinder/ckfinder-laravel-package/src/routes.php`:

```php
// vendor/ckfinder/ckfinder-laravel-package/src/routes.php

Route::any('/ckfinder/examples/{example?}', 'CKSource\CKFinderBridge\Controller\CKFinderController@examplesAction')
    ->name('ckfinder_examples');
```

After that you can navigate to the `<APP BASE URL>/ckfinder/examples` path and have a look at the list of available examples.
To find out about the code behind them, check the `views/samples` directory in the package (`vendor/ckfinder/ckfinder-laravel-package/views/samples/`).

### Including the Main CKFinder JavaScript File in Templates

To be able to use CKFinder on a web page you have to include the main CKFinder JavaScript file.
The preferred way to do that is to include the CKFinder setup template, as shown below:

```blade
@include('ckfinder::setup')
```

The included template renders the required `script` tags and configures a valid connector path.

```html
<script type="text/javascript" src="/js/ckfinder/ckfinder.js"></script>
<script>CKFinder.config( { connectorPath: '/ckfinder/connector' } );</script>
```

---

## Useful Links

 * [CKFinder 3 usage examples](https://ckeditor.com/docs/ckfinder/demo/ckfinder3/samples/widget.html)
 * [CKFinder 3 for PHP connector documentation](https://ckeditor.com/docs/ckfinder/ckfinder3-php/)
 * [CKFinder 3 Developer's Guide](https://ckeditor.com/docs/ckfinder/ckfinder3/)
 * [CKFinder 3 issue tracker](https://github.com/ckfinder/ckfinder)
