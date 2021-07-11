# php-thx
[![Release](https://img.shields.io/github/release/S1SYPHOS/php-thx.svg)](https://github.com/S1SYPHOS/php-thx/releases) [![License](https://img.shields.io/github/license/S1SYPHOS/php-thx.svg)](https://github.com/S1SYPHOS/php-thx/blob/main/LICENSE) [![Issues](https://img.shields.io/github/issues/S1SYPHOS/php-thx.svg)](https://github.com/S1SYPHOS/php-thx/issues) [![Status](https://travis-ci.org/S1SYPHOS/php-thx.svg?branch=main)](https://travis-ci.org/S1SYPHOS/php-thx)

A very simple PHP library for acknowledging the people behind your frontend dependencies - and giving thanks.


## Getting started

Install this package with [Composer](https://getcomposer.org):

```text
composer require S1SYPHOS/php-thx
```

**Note:**
For yarn v2 support, the `php-yaml` package is required!


## Usage

First, determine the paths to your files (datafile & lockfile, see below):

```php
<?php

require_once('vendor/autoload.php');

use S1SYPHOS\Thx;

$pkgFile = 'path/to/composer.json';                # or 'package.json' for NPM / Yarn
$lockFile = 'path/to/composer.lock'                # or 'package-lock.json' for NPM / 'yarn.lock' for Yarn
$cacheDriver = 'file';                             # Optional: Cache driver, see below
$cacheSettings = ['storage' => '/path/to/cache'];  # Optional: Cache settings, see below
```

**Note:**
For available cache drivers & settings, see [here](https://github.com/terrylinooo/simple-cache)!

Passing these options to `Thx::giveBack()` creates an instance:

```php
$obj = Thx::giveBack($pkgFile, $lockFile, $cacheDriver, $cacheSettings);
```

.. which you may configure to your liking by using:

- `setTimeout(int $seconds)`
- `setCacheDuration(int $days)`
- `setUserAgent(string $userAgent)`
- `setBlockList(array $blockList)`

```php
# For example:

$obj->setCacheDuration(7);                    # Cache results for one week
$obj->setBlockList(['php', 'some/library']);  # Block from being processed
```

After setting everything up, `spreadLove()` makes some API calls:

- Composer packages @ https://repo.packagist.org
- Node packages @ https://api.npms.io

```php
$obj->spreadLove();
```

Currently there are three methods you can use:

- `data()` returns raw data from lockfile for all used packages
- `pkgs()` returns processed data for all used packages
- `packages()` returns the names of all used packages

```php
# Alternative 1:
# Process data
$obj->spreadLove();

# Work with it
$raw = $obj->data();
$processed = $obj->pkgs();

# Alternative 2:
# Since `spreadLove` allows chaining, you could also do it like this:
$obj->spreadLove()->data();

# The second call provides cached data, no performance penalty there
$obj->spreadLove()->pkgs();
```


## Example

This example should get you started:

```php
<?php

require_once('vendor/autoload.php');

use S1SYPHOS\Thx;

$pkgFile = 'path/to/composer.json';  # or 'package.json' for NPM / Yarn
$lockFile = 'path/to/composer.lock'  # or 'package-lock.json' for NPM / 'yarn.lock' for Yarn

try {
    $obj = Thx::giveBack($pkgFile, $lockFile)->spreadLove();

    # Dump package names
    var_dump($obj->packages())

    # Dump (raw) data extracted from lockfiles
    var_dump($obj->data())

} catch (Exception $e) {
    # No dependencies found, file not found, ..
    echo $e->getMessage();
}
```


## Roadmap

- [x] ~~Add (more sophisticated) tests~~ for now, they get the job done
- [x] Parse yarn v1 lockfiles
- [x] Gather information using public APIs
- [x] Custom `Exception`s
- [ ] Provide more methods


## Credits

Most of the helper functions were taken from [Kirby](https://getkirby.com)'s excellent [`toolkit`](https://github.com/getkirby-v2/toolkit) package by [Bastian Allgeier](https://github.com/bastianallgeier) (who's just awesome, btw).


**Happy coding!**
