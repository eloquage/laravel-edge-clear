# Laravel Edge Clear

![Laravel Edge Clear cache network banner](art/banner.png)

Purge Cloudflare cache and set edge-cache headers from a Laravel application.

[![Tests](https://github.com/eloquage/laravel-edge-clear/actions/workflows/run-tests.yml/badge.svg)](https://github.com/eloquage/laravel-edge-clear/actions/workflows/run-tests.yml)
[![Latest Version](https://img.shields.io/packagist/v/illuma-law/laravel-edge-clear)](https://packagist.org/packages/illuma-law/laravel-edge-clear)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Requires PHP 8.3+ and Laravel 11, 12, or 13.

## Install and configure

```bash
composer require illuma-law/laravel-edge-clear
php artisan vendor:publish --tag=edge-clear-config
```

Set your Cloudflare zone and API token in `.env`:

```dotenv
CLOUDFLARE_ZONE_ID=your-zone-id
CLOUDFLARE_API_TOKEN=your-api-token
```

Purging is enabled only in production by default. Set
`EDGE_CLEAR_ONLY_IN_PRODUCTION=false` to enable it in other environments.
Never use real credentials in local or test environments.

## Purge cache

```php
use IllumaLaw\EdgeClear\Facades\CloudflarePurger;

CloudflarePurger::purgeByUrls([
    'https://example.com/posts/hello',
    'https://example.com/posts',
]);
```

You can also purge by tags or clear the whole zone:

```php
CloudflarePurger::purgeByTags(['posts', 'news']);
CloudflarePurger::purgeEverything();
```

The `SetEdgeCacheHeaders` middleware adds cache headers to successful GET
responses. The optional `BypassEdgeCache` middleware disables caching for a
route:

```php
use IllumaLaw\EdgeClear\Middleware\BypassEdgeCache;
use IllumaLaw\EdgeClear\Middleware\SetEdgeCacheHeaders;

Route::get('/posts', PostsController::class)
    ->middleware(SetEdgeCacheHeaders::class.':600;posts');

Route::get('/account', AccountController::class)
    ->middleware(BypassEdgeCache::class);
```

## Test

```bash
composer test
composer analyse
vendor/bin/pint --test
```

MIT licensed. See [LICENSE](LICENSE).
