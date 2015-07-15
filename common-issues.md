---
currentMenu: common-issues
---

# Common Issues

<div id="generated-toc"></div>

While installation should be pretty simple, here are some of the more common questions/issues people have.

-----

## The requested URL /auth/signin was not found on this server
Chances are that `mod_rewrite` is either not installed on your system, or has not been configured correctly for your virtualhost using AllowOverride.

__Troubleshooting:__
Add garbage text into the `public/.htaccess` file on your local install and hit the homepage again. If it bombs out (it should), then mod_rewrite is probably working. If it doesn't, it means your webserver isn't even looking for the .htaccess rules and you'll need to check your virtualhost config.

(Make sure to take the garbage out of the .htaccess file once you've gotten it sorted!)

-----

## Error message: mcrypt_encrypt(): Size of key is too large for this algorithm

In `app/production/config/app.php`, find the `key` on line 43. Manually change that to a random string that is __no more than 32 characters__.

You can also run

```
php artisan key:generate
```

to auto-generate a key for you.

-----

## White page with error: Error in exception handler.

Make sure you've changed the permissions on the `app/storage` directory and all of the directories within.

-----

## Database [] not configured

This happens when you think you're running the app in a different environment than your app thinks it's running in. The most common reason for this is you either forgot to edit `bootstrap/start.php` to include your hostname in whichever environment you want to run it as, or the hostname you entered there is incorrect.
Snipe-IT will always default to production if it can't figure out what mode to run in.

To find your hostname, type `hostname` in a terminal prompt and copy the output to whichever of the environments in `bootstrap/start.php` that you want to run it in. The app examines this array, and if it finds a hostname there that matches the actual hostname, that's the environment it will run in.

Alternatively, you can try just copying your config files over into `app/config/production` and just letting it default to production if you don't plan on doing any code development.

-----

## FatalErrorException. Syntax error, unexpected '[', expecting ')'

This error occurs when you're using an older version of PHP and trying to run Snipe-IT.
Per the PHP manual:

As of PHP 5.4 you can also use the short array syntax, which replaces array() with [].
PHP 5.4 is the minimum requirement in Snipe-IT, so you must upgrade your version of PHP to resolve this error.

You should also delete the contents of the cached views in `app/storage/views` once you upgrade PHP, to resolve any cached issues.

-----

## Error message: Error Output: PHP Fatal error: Call to undefined method IlluminateFoundationApplication::registerCoreContainerAliases() in ../src/Illuminate/Foundation/start.php on line 106

Remove `bootstrap/compiled.php` and your vendors dir, and try running `php composer.phar update`.

-----

## Image Source not readable

This usually means that the temporary directory is not writable by the web server. Check the permissions section of the documentation for your server OS and make sure you've granted the user the ability to write to the temp directory.

-----

## While running composer: intervention/image dev-master requires ext-fileinfo * -> the requested PHP extension fileinfo is missing or not enabled on your system.

As the error states, your server is missing the `fileinfo` extension, which is one of the requirements for running Snipe-IT.

Windows users must include the bundled `php_fileinfo.dll `DLL file in `php.ini` to enable this extension. To enable Fileinfo, add or uncomment this line in your php.ini:

```
extension=php_fileinfo.dll
```

and restart the web server.

Linux users need to add or uncomment the following in their `php.ini`:

```
extension=fileinfo.so
```

and restart the web server.

-----

## During composer install, it's asking me for my Github credentials

This is an artifact of Github having a very low API rate limit for unauthenticated accounts. Make sure you're using the `--prefer-source` flag when doing your composer installs and updates.

-----

## Call to undefined method IlluminateCookieCookieJar::get()

If you're using Snipe-IT 1.2.6 or earlier, grab the latest off of the develop branch and run `php composer install`, or edit your composer.json to use Sentry 2.1.* and then run `php composer update`.

-----

## Installing d11wtq/boris (v1.0.8) Downloading: connection... Failed to download d11wtq/boris from dist

This error is due to the fact that the repo of the dependency required by one of the libraries has moved, and composer has cached it's old location (where it doesn't exist anymore.) Composer may be caching the vendor location, even though the entry on Packagist was updated by the REPL author.

To solve this, clear your composer cache by running `php composer clear-cache`.

If this doesn't work, Try removing the `composer.lock` file from the main Snipe-IT directory, and running `php composer install`.