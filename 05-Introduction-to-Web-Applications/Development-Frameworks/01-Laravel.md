# Laravel — PHP Web Framework

Laravel is an MIT-licensed PHP framework with MVC-style architecture, routing, middleware, validation, Blade templates, Eloquent ORM, migrations, queues and authentication integrations. PHP executes application logic; Composer manages PHP packages; Artisan is Laravel's CLI. A typical request flows through public/index.php, middleware, router, controller, model and response. The public directory is the document root; do not serve the project root.

## Files and commands
routes/web.php defines web routes; routes/api.php may be created/configured for API routes depending on the project version; app/Http/Controllers contains controllers; app/Models contains models; database/migrations defines schema changes; resources/views contains Blade templates; .env configures environment settings and must stay private.

```bash
php -v
composer --version
php artisan route:list
php artisan migrate:status
```
These show runtime/client versions, routes and migration status respectively in an existing Laravel project. Do not run migrations against a real database without review. No installation is required.

## Original route example
In routes/web.php of a local demonstration project:
```php
use Illuminate\Support\Facades\Route;

Route::get('/api/demo-notices/{id}', function (int $id) {
    abort_unless($id === 42, 404);
    return response()->json(['id' => $id, 'content' => 'Class notice']);
})->whereNumber('id');
```
Route::get registers GET; {id} is a route parameter; whereNumber restricts matching; abort_unless returns an error unless the condition holds; response()->json returns JSON. This public fake-data example needs no real student data. Real protected endpoints need auth and policy checks.

## Security and project uses
Use validation, Eloquent/bound query values, policies and gates, CSRF protection for web forms, Blade's escaped output, safe upload handling and production error settings. Avoid unrestricted mass assignment, raw SQL interpolation and exposed .env files. Laravel could host a future PHP school portal; UniTrack remains Flask. [Official docs](https://laravel.com/docs), [Composer](https://getcomposer.org/doc/).
