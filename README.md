# Laravel Doorkeeper

I would appreciate you taking the time to look at my [Patreon](https://www.patreon.com/faustbrian) and considering to support me if I'm saving you some time with my work.

This package counts the amount of records on a relationship and compares them with a given set of limits. This can be useful to determine if a user has reached the limit of files he can upload or something similar.

## Installation

Require this package, with [Composer](https://getcomposer.org/), in the root directory of your project.

``` bash
$ composer require faustbrian/laravel-doorkeeper
```

## Usage

#### Model
``` php
<?php
namespace App;

use BrianFaust\Doorkeeper\Traits\Doorkeeper;
use BrianFaust\Doorkeeper\Contracts\DoorkeeperContract;

class User extends Model implements DoorkeeperContract
{
    use Doorkeeper;


    public $limits = [
        'posts' => 5,
        'files' => 10,
    ];

    public function posts() {
        return $this->hasMany('App\Post');
    }

    public function files() {
        return $this->hasMany('App\File');
    }
}
```

#### Middleware
``` php
<?php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Auth;

class ReachedLimits
{

    public function handle($request, Closure $next)
    {
        $user = Auth::check();

        if ( $user->limits($user->subscription->limits)->fails() ) {
            return redirect()->route('billing');
        }

        return $next($request);
    }
}
```

#### Controller
``` php
<?php
namespace App\Http\Controllers;

class DashboardController
{

    public function __construct()
    {
        $this->middleware('reachedLimits');
    }


    public function index()
    {
        return view('dashboard');
    }
}
```

## Methods

#### Perform checks with custom limits.
``` php
$user->limits($user->subscription->limits)->passes();
$user->limits($user->subscription->limits)->fails();
```

#### Perform a check and see if it passes.
``` php
$user->passes();
```

#### Perform a check and see if it fails.
``` php
$user->fails();
```

#### Get all limits.
``` php
$user->allowed();
```

#### Get a specific limit.
``` php
$user->allowed('posts');
```

#### Get the current counter.
``` php
$user->current();
```

#### Get a specific counter.
``` php
$user->current('posts');
```

#### Check if the overall limit has been reached.
``` php
$user->maxed();
```

#### Check if a specific limit has been reached.
``` php
$user->maxed('posts');
```

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Testing

``` bash
$ phpunit
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover a security vulnerability within this package, please send an e-mail to Brian Faust at hello@brianfaust.de. All security vulnerabilities will be promptly addressed.

## Credits

- [Brian Faust](https://github.com/faustbrian)
- [All Contributors](../../contributors)

## License

[MIT](LICENSE) © [Brian Faust](https://brianfaust.de)
