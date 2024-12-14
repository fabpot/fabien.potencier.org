---
date: '2017-04-13'
title: 'Symfony 4: Automate your Workflow'
summary: |
    Time to introduce Symfony Flex, Symfony 4 secret weapon. Learn more about
    what it can do for you.
params:
    author: Fabien Potencier
url: /symfony4-workflow-automation.html
---

Symfony 4's most "innovative" feature is the way it drives the day-to-day
application management. No more tedious copy/paste from README files. No more
boilerplate code. Automation to the max. On a curated list of Composer packages.

Symfony Flex
------------

Symfony 4 is powered by Symfony Flex, a deceptively simple but powerful
Composer plugin. The [default Symfony
skeleton](https://github.com/symfony/skeleton) only lists two main
dependencies: `symfony/flex` and `symfony/framework-bundle`. Everything else is
optional.

As you can imagine, most projects need more than `FrameworkBundle`. And
installing and configuring all packages and bundles by hand is error-prone,
tedious, and just plain boring. Even for the few dependencies that Symfony
Standard Edition depends on.

This is where Symfony Flex shines.

When you install a Composer package, Flex hooks into the installation process
to automate its integration into your project via pre-defined and
community-driven recipes. It works for any Composer packages, not just Symfony
bundles. If a recipe does not exist, you can still configure it the old way,
like you currently do.

Take `sensiolabs/security-checker` as an example. It works for any PHP
projects. But when installed on a project using Symfony Flex, it knows how to
run the checker whenever you run `composer install`.

The automation is implemented in a recipe that has a JSON manifest that
describes how to integrate the package into a Symfony application. Here is the
one for `sensiolabs/security-checker`:

```json
{
    "copy-from-recipe": {
        "config/": "%CONFIG_DIR%/"
    },
    "composer-scripts": {
        "vendor/bin/security-checker security:check": "php-script"
    }
}
```

Flex currently implements 9 "configurators" that helps automate the integration
of any PHP package into a Symfony application: `copy-from-recipe`,
`copy-from-package`, `bundles`, `env`, `container`, `makefile`,
`composer-scripts`, `gitignore`, and `post-install-output`.

Bundles
-------

The `bundles` configurator enables bundles in your project by adding them
to the `bundles.php` file (and it removes them when you uninstall the dependency):

```json
{
    "bundles": {
        "Symfony\\Bundle\\DebugBundle\\DebugBundle": ["dev", "test"],
        "Symfony\\Bundle\\MonologBundle\\MonologBundle": ["all"]
    }
}
```

Configuration
-------------

Configuration can be added via the `copy-from-recipe` and `copy-from-package`
configurators: the former copies files from the recipe while the later copies
files from the Composer package itself.

```json
{
    "copy-from-package": {
        "bin/check.php": "%BIN_DIR%/check.php"
    },
    "copy-from-recipe": {
        "config/": "%CONFIG_DIR%/",
        "src/": "%SRC_DIR%/"
    }
}
```

Environment Variables
---------------------

The `env` configurator knows how to add environment variables to the project's
`.env` and `.env.dist` files (and removes them when uninstalling the dependency):

```json
{
    "env": {
        "APP_ENV": "dev",
        "APP_DEBUG": "1"
    }
}
```

Makefile Tasks
--------------

The `makefile` configurator adds new tasks to the project's `Makefile` (and
removes them when uninstalling the dependency). Unlike other configurators,
there is no entry in the `manifest.json`. Just define tasks in a `Makefile`
file:

```makefile
cache-clear:
	@test -f bin/console && bin/console cache:clear --no-warmup || rm -rf var/cache/*
.PHONY: cache-clear

cache-warmup: cache-clear
	@test -f bin/console && bin/console cache:warmup || echo "cannot warmup the cache (needs symfony/console)"
.PHONY: cache-warmup
```

*Note*: A previous version of this blog post described a previous version where
the post install output was defined in the `manifest.json` file.

Composer Scripts
----------------

The `composer.json` file defined in `symfony/skeleton` contains the following snippet:

```json
{
    "scripts": {
        "auto-scripts": [
        ],
        "post-install-cmd": [
            "@auto-scripts"
        ],
        "post-update-cmd": [
            "@auto-scripts"
        ]
    }
}
```

The `composer-scripts` configurator manages the special `@auto-scripts` section
by adding commands and scripts defined in recipes. Those are automatically run
on `composer install` and `composer update`:

```json
{
    "composer-scripts": {
        "assets:install --symlink --relative %PUBLIC_DIR%": "symfony-cmd"
    }
}
```

`.gitignore`
------------

The `.gitignore` configurator adds patterns to the `.gitignore` file. And like
for the other configurators, it knows how to clean up when you remove a package:

```json
{
    "gitignore": [
        "/phpunit.xml"
    ]
}
```

`post-install-output`
---------------------

If you want to display some next step actions when a package is installed, list
them via the `post-install-output` configurator. Like for the Makefile, the output
must be defined in a `post-install.txt`:

```txt
Execute <fg=blue>make serve</> to run your application.
```

*Note*: A previous version of this blog post described a previous version where
the post install output was defined in the `manifest.json` file.

Full Example
------------

The most "complex" manifest is currently the one for `symfony/framework-bundle`
and reads as follows:

```json
{
    "bundles": {
        "Symfony\\Bundle\\FrameworkBundle\\FrameworkBundle": ["all"]
    },
    "copy-from-recipe": {
        "config/": "%CONFIG_DIR%/",
        "public/": "%PUBLIC_DIR%/",
        "src/": "%SRC_DIR%/"
    },
    "composer-scripts": {
        "make cache-warmup": "script",
        "assets:install --symlink --relative %PUBLIC_DIR%": "symfony-cmd"
    },
    "env": {
        "APP_ENV": "dev",
        "APP_DEBUG": "1",
        "APP_SECRET": "%generate(secret)%"
    },
    "gitignore": [
        ".env",
        "/public/bundles/",
        "/var/",
        "/vendor/"
    ]
}
```

That's all. I have yet to find a case not covered by this small set of
configurators. Note that I might remove the `container` one as it is not used
anymore in the current set of recipes I have written so far.

An Opinionated Repository
-------------------------

The Symfony Flex recipe repository will be opinionated. The developer is free
to use any combinations of packages, but to be able to provide a good sensible
configuration, we need to make choices. We won't be able to support all
bundles, only a curated set of them. Which is probably a good thing as well.

Symfony doesn't make choices for you. And that makes sense. You know what you
need for your project. But with more than 3000 bundles to choose from,
selecting the right bundle from the ecosystem is far from being easy for
beginners. Want an admin generator? Currently, you need to choose between 26
different ones. And several are quite popular. Which one should I use?

Compare that to symfony1, where the admin generator was built-in. This is just
one example. Have a look at the number of Blog bundles, or API ones. The great
news is that the Symfony community is hard at work.

Along the years, Symfony became a very low-level framework, without batteries
included. Now that Symfony gives us a very powerful platform to work with, I
think we can work on being more opinionated again.

We should be giving more exposure to good bundles. In order to do that, we
would first need to define what "good" means. I can imagine some simple rules
like the license, or the number of opened bugs vs activity on the Git
repository, the amount of documentation, the number of contributors, whether it
is compatible with a large set of Symfony versions, and so on.

And of course, any bundle can still be installed the old way, without
auto-configuration.

The Symfony Flex Server
-----------------------

Besides being a Composer plugin, Symfony Flex also communicates with an HTTP
server to get up-to-date information about package recipes, aliases, and more.

Try `curl https://flex.symfony.com/recipes/sensiolabs/security-checker/4.0 |
json_pp`.

Eager to try out Symfony Flex? Well, I will push everything to Github just in
time for the next blog post.
