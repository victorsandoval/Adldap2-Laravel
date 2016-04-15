# Quick Start - From Scratch

Here is a step by step guide for configuring Adldap2 with a fresh new laravel project. This guide assumes you have
knowledge working with Laravel, Active Directory, LDAP Protocol and command line tools (such as Composer and Artisan).

This guide is thanks to @st-claude.

1. Create a new laravel project by running `laravel new`. [Laravel Installation](https://laravel.com/docs/5.2#installation)

2. Insert `"adldap2/adldap2-laravel": "2.0.*"` in your `composer.json` file in the root directory of your project.

3. Run `composer update` to pull in Adldap2 and its dependencies.

4. Create a new database in your desired database interface (such as PhpMyAdmin, MySQL Workbench etc.)

5. Enter your database details and credentials inside the `.env` file located in your project root directory.

6. If you're using Active Directory username's to login users **instead** of their emails, you will need to change
   the default `email` column in `database/migrations/2014_10_12_000000_create_users_table.php`.

   * From: `$table->string('email')->unique();`
   * To: `$table->string('username')->unique();`

7. Now run `php artisan migrate`.

8. Insert the following providers in your `config/app.php` file:

   * `Adldap\Laravel\AdldapServiceProvider::class`,
   * `Adldap\Laravel\AdldapAuthServiceProvider::class`,

9. Now, insert the facade into your `config/app.php` file:

   * `'Adldap' => Adldap\Laravel\Facades\Adldap::class`,

10. Now run `php artisan vendor:publish` to publish Adldap2's configuration files.

    *  Two files will be published inside your `config` folder, `adldap.php` and `adldap_auth.php`.

11. Modify the `config/adldap.php` file for your AD server configuration.

12. Run the command `php artisan make:auth` to scaffold authentication controllers and routes.

13. Inside the generated `app/Http/Controllers/AuthController.php`, you'll need to change the `protected $username`
    property if you're logging in users by username. Otherwise ignore this step.

    ```
    class AuthController extends Controller
    {
        protected $username = 'username';
    ```

14. Now insert a new auth driver inside your `config/auth.php` file:

    ```
    'guards' => [
        'web' => [
            'driver'   => 'session',
            'provider' => 'adldap', // Switched to 'adldap'
        ],

        'api' => [
            'driver'   => 'token',
            'provider' => 'users',
        ],
    ],


    'providers' => [
        // New 'adldap' provider:
        'adldap' => [
            'driver' => 'adldap',
            'model'  => App\User::class,
        ],

        'users' => [
            'driver' => 'eloquent',
            'model'  => App\User::class,
        ],
    ],
    ```

15. Inside your `resources/views/auth/login.blade.php` file, if you're requiring the user logging in by username, you'll
    need to modify the HTML input to `username` instead of `email`:

    From:
    ```
    <input type="email" class="form-control" name="email" value="{{ old('email') }}">
    ```

    To:

    ```
    <input type="username" class="form-control" name="username" value="{{ old('username') }}">
    ```

16. You should now be able to login to your Laravel application using LDAP authentication!