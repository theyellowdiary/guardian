[![Build Status](https://travis-ci.org/rennokki/guardian.svg?branch=master)](https://travis-ci.org/rennokki/guardian)
[![codecov](https://codecov.io/gh/rennokki/guardian/branch/master/graph/badge.svg)](https://codecov.io/gh/rennokki/guardian/branch/master)
[![StyleCI](https://github.styleci.io/repos/136514812/shield?branch=master)](https://github.styleci.io/repos/136514812)
[![Latest Stable Version](https://poser.pugx.org/rennokki/guardian/v/stable)](https://packagist.org/packages/rennokki/guardian)
[![Total Downloads](https://poser.pugx.org/rennokki/guardian/downloads)](https://packagist.org/packages/rennokki/guardian)
[![Monthly Downloads](https://poser.pugx.org/rennokki/guardian/d/monthly)](https://packagist.org/packages/rennokki/guardian)
[![License](https://poser.pugx.org/rennokki/guardian/license)](https://packagist.org/packages/rennokki/guardian)


# Eloquent Guardian
Eloquent Guardian is your big beast that prevents anyone from accessing restricted content. It is like a bodyGUARD(ian), but it's less harmful.

# Installation
Install the package:
```bash
$ composer require rennokki/guardian
```

If your Laravel version does not support package discovery, add this line in the `providers` array in your `config/app.php` file:
```php
Rennokki\Guardian\GuardianServiceProvider::class,
```

Publish the config file & migration files:
```bash
$ php artisan vendor:publish
```

Migrate the database:
```bash
$ php artisan migrate
```

Add the `HasPermissions` trait to your Eloquent model:
```php
use Rennokki\Guardian\Traits\HasPermissions;

class User extends Model {
    use HasPermissions;
    ...
}
```

You can allow, disallow, prohibit or unprohibit permissions.

There are more permission types. Here are they:

* String Type. It's just a string, it's not related to any model.
```php
$user->allow('edit-posts');
$user->can('edit-posts');
```

* Global Type. It's related to a model, but not to a specific one.
```php
$user->allow('edit', App\Post::class);
$user->can('edit', App\Post::class);
```

* Global Specific Type. It's related to a specific model.
```php
$user->allow('edit', App\Post::class, 'post_id_here');
$user->can('edit', App\Post::class, 'post_id_here');
```

I will get through methods you can use with this package:
* `hasPermission()`
* `getPermission()`
* `can()`
* `cannot()`
* `cant()`
* `allow()`
* `disallow()`
* `deletePermission()`
* `prohibit()`
* `unprohibit()`

```php
$user = User::find(1);

// Relationships.
$user->permissions(); 
$user->allowedPermissions();
$user->prohibitedPermissions();

// Check if the user can.
$this->can('edit-articles');
$this->can('edit', App\Post::class);
$this->can('edit', App\Post::class, 'post_id_here');

// Check if the user cannot.
$this->cannot('edit-articles');
$this->cannot('edit', App\Post::class);
$this->cannot('edit', App\Post::class, 'post_id_here');

// An alias of the cannot() method.
$this->cant('edit-articles');
$this->cant('edit', App\Post::class);
$this->cant('edit', App\Post::class, 'post_id_here');

// Allow a permission.
// If there is an active record, it will automatically set its prohibited status to false.
// If you have prohibited the permission earlier and you then call allow(), then the can() will return true.
$this->allow('edit-articles');
$this->allow('edit', App\Post::class);
$this->allow('edit', App\Post::class, 'post_id_here');

// If you allow a permission on a global one.
$this->allow('edit', App\Post::class);

// When you will check against any id of the class, you will get true.
$this->can('edit', App\Post::class, 'post_id_here'); // true

// Disallow a permission. This will delete the permission.
// Use prohibit() to keep it but to not delete it.
// If the user does not have an active record of the permission, it will create the permission with is_prohibited to 1 and return it.
$this->disallow('edit-articles');
$this->disallow('edit', App\Post::class);
$this->disallow('edit', App\Post::class, 'post_id_here');

// This is an alias of the disallow() method.
$this->deletePermission('edit-articles');
$this->deletePermission('edit', App\Post::class);
$this->deletePermission('edit', App\Post::class, 'post_id_here');

// Use prohibit/unprohibit to keep it but not delete the record.
// Note: If the user does not have an active record of the permission, it will create one with is_prohibited to 1 and reurn it.
$this->prohibit('edit-articles');
$this->prohibit('edit', App\Post::class);
$this->prohibit('edit', App\Post::class, 'post_id_here');
$this->unprohibit('edit-articles');
$this->unprohibit('edit', App\Post::class);
$this->unprohibit('edit', App\Post::class, 'post_id_here');
```

# Middleware
You can use the methods within the model as-is, or you can use a middleware.

For this, you should add the middleware to your `$routeMiddleware` array from `app\Http\Kernel.php`

```php
'guardian' => \Rennokki\Guardian\Middleware\CheckPermission::class,
```

After this, you can use it in your routes to filter the bad bugs :)

You can use it with simple permissions:
```php
Route::get('/admin', 'AdminController@ControlPanel')->middleware('guardian:access-admin-panel');
```

Or you can use it for a global model:
```php
Route::post('/admin/products', 'AdminController@CreateProduct')->middleware('guardian:create,App\Product');
```

This middleware accepts a third parameter, so this works even with Global Specific permissions.

This is a little bit complicated, because it involves the **route parameter** instead of the **model ID**.

Let's use an example for this:
```php
Route::patch('/admin/{post_id}', 'AdminController@EditPost')->middleware('guardian:edit,App\Post,post_id');
```

The third parameter is the placeholder name, in the request route, where the ID of the model will be placed.

For all three types of routes, exceptions will be thrown if there are not enough permissions:

* `Rennokki\Guardian\Exceptions\PermissionException`, if no permissions.
* `Rennokki\Guardian\Exceptions\RouteException`, if the passed route parameter is non-existent.

You can access `permission()`, `modelType()` and `modelIdPlaceholder()` methods within the exception to handle your exception further.

## Support on Beerpay
Hey dude! Help me out for a couple of :beers:!

[![Beerpay](https://beerpay.io/rennokki/guardian/badge.svg?style=beer-square)](https://beerpay.io/rennokki/guardian)  [![Beerpay](https://beerpay.io/rennokki/guardian/make-wish.svg?style=flat-square)](https://beerpay.io/rennokki/guardian?focus=wish)