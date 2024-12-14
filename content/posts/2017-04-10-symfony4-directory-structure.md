---
date: '2017-04-10'
title: 'Symfony 4: Directory Structure'
summary: |
    Symfony 4 has a slightly reworked directory structure. Incremental
    adjustments to support new features and best practices.
params:
    author: Fabien Potencier
url: /symfony4-directory-structure.html
---

Symfony 3 came with a slightly different directory structure than Symfony 2.
Symfony 4 will also come with a reworked directory structure. Mostly
incremental adjustments to support new features and best practices.

The Symfony 3 [directory
structure](https://github.com/symfony/symfony-standard/issues/584#issuecomment-23148928)
introduced a more standard Unix-like directory structure, with less sub-directories.
Symfony 4 keeps going in that direction.

Using well-known directory names helps. Using `bin/`, `src/`, or `var/` was a
great step forward. Symfony 4 adds `etc/` in place of `app/`. It was proposed
for Symfony 3 but rejected for reasons that don't hold anymore.

**Update**: The `etc/` has been changed to `config/` after a long discussion
with the community. The `web/` directory has also been changed to `public/`.
The blog posts about Symfony 4 have been updated to reflect these changes.

Tests under `tests/`
--------------------

Tests move to a top-level `tests/` directory. It was proposed before, it just
makes sense now that we have bundle-less applications. It also allows to
declare a specific test namespace for autoloading:

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "App\\Tests\\": "tests/"
        }
    }
}
```

Templates under `templates/`
----------------------------

Templates become first-class citizens via a new top-level `templates/`
directory. Would `tpl` make more sense compared to the other short 3-letter
directory names?

Why not `views`? `views` was the original name I chose very early on because it
was shorter than `templates`. It was a mistake as `View` is a concept by itself.
Fixed, finally.

Having templates at the root level also makes working with web designers
easier. And this directory is only created when you install Twig.

Moving templates allows to reserve `src/` for PHP classes only, no more
resources. Another consequence of being bundle-less.

Configuration under `config/`
-----------------------------

The new `config/` directory is the equivalent of the current `app/config/`
directory. But with a very different layout. `parameters.yml` and
`parameters.yml.dist` are gone.

The main entry point for the container is an empty `container.yaml` that you
can use to define your own services and parameters. Configuration for bundles
that you install via Composer are stored as **individual** files. One file per
bundle and per environment. The same goes for the routing configuration. That's
a soft requirement for auto-configuration, but allows for a better file
organization.

Configuration files can be written in PHP, XML, or YAML as before. The only
difference being the usage of the standard `.yaml` extension instead of the
current `.yml` one.

One major change is the introduction of a `bundles.php` file where installed
bundles are referenced.

One file per bundle and `bundles.php` are the two core concepts that enables
Symfony to automatically manage your bundles and their configurations.

Source Code under `src/`
------------------------

The `Kernel` class has been moved to `src/`, where it belongs. The content is
very different from the one in Symfony 2. First, it uses the `MicroKernelTrait`
trait. Then, it implements the logic to load the bundles from `bundles.php` and
to read the various bundle configuration files. The logic works as of Symfony
3.3. For instance, the code that loads the container configuration files reads
as follow:

```php
protected function configureContainer(ContainerBuilder $container, LoaderInterface $loader)
{
    $confDir = dirname(__DIR__).'/config';
    $loader->import($confDir.'/packages/*'.self::CONFIG_EXTS, 'glob');
    if (is_dir($confDir.'/packages/'.$this->getEnvironment())) {
        $loader->import($confDir.'/packages/'.$this->getEnvironment().'/**/*'.self::CONFIG_EXTS, 'glob');
    }
    $loader->import($confDir.'/container'.self::CONFIG_EXTS, 'glob');
}
```

Temporary files under `var/`
----------------------------

`var/` is very similar to Symfony 3, with some minor tweaks in the best
practices.

`var/cache/` should now only be used to store long term cached contents like
compiled container files, compiled translations, or Doctrine proxies. No
temporary files. Basically, anything stored under `var/cache` should have a
warmup class able to generate the cache files. Files that should never be
updated after deployment to allow for read-only filesystems.

What about temporary files then? Use another directory like `var/tmp/`. Or just
the standard Unix `/tmp` directory.

If you follow this best practice, we will be able to guarantee a read-only
`/var/cache` directory at some point. Having read-only directories is a
requirement of some hosting platforms like Heroku or SensioCloud and helps
scale an application. Probably not for Symfony 4.0 though.

Web files under `public/`
-------------------------

I have already mentioned the single web front controller under `public/`.
Almost all the other files were removed. No `config.php`. No `.htaccess`. No
`favicon.ico` or `apple-touch-icon.png`. Not even `robots.txt`. Not all
projects need those files. If you want skeletons for those files, I got you
covered of course.

Everything is optional
----------------------

One difference with Symfony 3 is that all first-level directories are optional.
Don't use templates for you API? No need to create a `templates/` directory.
Directories are created on-demand anyway.

When using Composer to add a new bundle, Symfony 4 levers this new directory
structure to automatically register the bundle in `bundles.php`, copy some
sensible configuration under `config/`, and much more. What else you ask? Great
topic for the next post.
