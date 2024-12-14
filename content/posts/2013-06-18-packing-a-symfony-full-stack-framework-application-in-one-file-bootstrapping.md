---
date: '2013-06-18'
title: Packing a Symfony full-stack Framework Application in one File -- Bootstrapping
summary: |
    In this second part of the series, we are going to install the Symfony Standard Edition and start cleaning it as much as possible.
params:
    author: Fabien Potencier
url: /packing-a-symfony-full-stack-framework-application-in-one-file-bootstrapping.html
aliases:
    - /packing-a-symfony-full-stack-framework-application-in-one-file-bootstrapping
    - /article/70/packing-a-symfony-full-stack-framework-application-in-one-file-bootstrapping
---

{{< notice note >}}
This article is part of a series of articles that explains how to pack a Symfony
full-stack application in one file. The first article explains why this might actually
be useful:
1) [Introduction](https://fabien.potencier.org/article/69/packing-a-symfony-full-stack-framework-application-in-one-file-introduction),
2) [Bootstrapping](https://fabien.potencier.org/article/70/packing-a-symfony-full-stack-framework-application-in-one-file-bootstrapping),
...
{{< /notice >}}

The most common way to create a Symfony project is to start with the Symfony
[Standard Edition](http://symfony.com/download): it defines a sensible
directory structure for your project and it make things a lot easier when
someone want to take over an existing project as he knows where the templates,
the controllers, or the configuration are stored. So, let's start our journey
with the Symfony Standard Edition:

    $ composer.phar create-project -n symfony/framework-standard-edition se/ 2.3.0

From there, let's remove a bunch of code to get the bare minimum of code
needed to keep it working:

    rm -rf LICENSE README.md UPGRADE* bin/ app/SymfonyRequirements.php \
           app/autoload.php app/bootstrap.php.cache app/AppCache.php app/check.php \
           app/console app/phpunit.xml.dist app/Resources src/ web/config.php \
           web/favicon.ico web/robots.txt web/apple-touch-icon.png web/app.php web/bundles/ \
           app/cache/* app/log/* .travis.yml app/.htaccess web/.htaccess

I've removed all those files and directories because there are not needed for
the purpose of our challenge.

Next, let's simplify the configuration and move everything into just one file:

    # app/config/config_dev.yml
    framework:
        secret:          $ecret
        router:
            resource: "%kernel.root_dir%/config/routing_dev.yml"
        form:            ~
        csrf_protection: ~
        validation:      { enable_annotations: true }
        templating:
            engines: ['twig']
        session:         ~
        fragments:       ~

The `routing_dev.yml` file has been emptied for now, and all other
configuration files have been removed.

We can also remove most of the bundles from the application kernel class:

    use Symfony\Component\HttpKernel\Kernel;
    use Symfony\Component\Config\Loader\LoaderInterface;

    class AppKernel extends Kernel
    {
        public function registerBundles()
        {
            return array(
                new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
                new Symfony\Bundle\TwigBundle\TwigBundle(),
            );
        }

        public function registerContainerConfiguration(LoaderInterface $loader)
        {
            $loader->load(__DIR__.'/config/config_'.$this->getEnvironment().'.yml');
        }
    }

We can also simplify the `web/app_dev.php` code:

    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Component\Debug\Debug;

    require_once __DIR__.'/../vendor/autoload.php';
    Debug::enable();

    require_once __DIR__.'/../app/AppKernel.php';

    $kernel = new AppKernel('dev', true);
    $request = Request::createFromGlobals();
    $response = $kernel->handle($request);
    $response->send();
    $kernel->terminate($request, $response);

The `composer.json` file can also be simplified:

    {
        "name": "fabpot/onefile-challenge",
        "license": "MIT",
        "type": "project",
        "autoload": {
            "psr-0": { "": "src/" }
        },
        "require": {
            "php": ">=5.3.3",
            "symfony/symfony": "2.3.*"
        }
    }

{{< notice note >}}
As the code is available in a Git repository, I've kept the `.gitignore`
file with the following content:

       /app/cache/*
       /app/logs/*
       /vendor/
       /composer.lock
{{< /notice >}}

Run composer again to remove all unneeded dependencies:

    $ composer.phar update

After cleaning everything, here is the directory structure:

    app/
    ??? AppKernel.php
    ??? cache
    ??? config
    ?   ??? config_dev.yml
    ?   ??? routing_dev.yml
    ??? logs
    composer.json
    vendor/
    web/
    ??? app_dev.php

As you can see, we are now down to 5 files. And of course, you still have the
full power of the full-stack framework (from the web profiler to the Twig
templating system). Code for today is available on
[Github](https://github.com/fabpot/onefile-challenge/tree/Bootstrapping).

In the next post, I will write about the Kernel class and how it works.



