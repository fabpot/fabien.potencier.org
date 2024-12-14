---
date: '2017-04-05'
title: 'Symfony 4: Monolith vs Micro'
summary: |
    Monolith projects versus micro-applications; Symfony 4 supports both.
params:
    author: Fabien Potencier
url: /symfony4-monolith-vs-micro.html
---

*This is the second installment in a series of articles about Symfony 4. The
first one was about [the current limitations of the Symfony Distribution
model](/symfony4-compose-applications.html).*

Monolith projects versus micro-applications; a never-ending debate. Both ways
to develop applications are fine in my book. Symfony supports both. Even if the
Symfony Standard Edition is probably more suitable for monolith projects as it
depends on the `symfony/symfony` package. This meta-package contains all the
Symfony components and some "core" bundles. Among other features, you get Twig
and the Web Profiler. But technically, you don't need them if you are
developing an API for your next mobile application.

Of course, some extra files under `vendor/` don't change anything, but some
developers think that their applications are more "bloated" because of those
extra bits on their disk. It feels like those extra unused features make your
application fat. This is *mostly* about *perception* though.

Silex took another approach where each individual components are required when
needed. Does it make Silex simpler, more lightweight, or faster than Symfony?
No. Nevertheless, Symfony 4 is going to be more similar to Silex in this regard.

Symfony Applications go minimal
-------------------------------

Out is the dependency on `symfony/symfony`. A Symfony application will now
depend on the individual Symfony components and bundles. This is probably the
most significant change in the way you will develop applications with Symfony 4.
The main reason is not because it helps reducing the number of files under
`vendor/` though. It opens up many great opportunities.

It helps with **auto-configuration**, one major Symfony 4 feature. When
installing a bundle, Symfony will now configure it automatically with good
defaults. Configuration also adapts according to your other dependencies.

Take the Symfony Framework Bundle as an example. Some features can be enabled
or disabled, like form, validation, templating, serializer, assets, ...

As of Symfony 3.2, form support is enabled by default, but you can disable it
if you are not using it in your application. Why is it enabled by default?
Because there is no way for Symfony to know if you will use forms in your
application. Disabling it by default would lead to a slightly worse developer
experience as that's one configuration knob to tweak before your first form
works.

Now, imagine an application where `symfony/symfony` is not a dependency. An
application can start with just `symfony/framework-bundle`. Automatically
enabling form support is now trivial: enable it when `symfony/form` is
installed, disable it otherwise. Simple, no magic, no configuration, great
developer experience. You will love Symfony 4.

As a great side-effect, performance is better as optional features are
automatically disabled. Without any tweaks in configuration files. I will share
some simple benchmarks in another post.

Going minimal also means that Symfony does not try to make assumptions anymore
about your stack. Besides the removal of the `LICENSE` or `README` files in the
skeleton, there is no Apache `.htaccess` files anymore. What if you are using
Apache then? Well, you will have to wait for another post to get the answer,
but I got you covered.

Composition
-----------

Removing the dependency on `symfony/symfony` helps with another Symfony 4
feature: **auto-discovery** and dependency removals. I mentioned composition
yesterday. Symfony 4 will lever those smaller dependencies to help you compose
your applications.

Want to use forms? Require `symfony/form`. Want to use the PHP built-in server?
Require `symfony/web-server-bundle`. Progressive enhancement. Add dependencies
only when needed.

Coupled with auto-configuration, it is very powerful. Don't need the built-in
web server anymore? Remove the `symfony/web-server-bundle` package. Symfony
takes care of un-configuring the bundle.

This is a very simple example, but there is more. Let's illustrate the power of
this new paradigm with
[SensioDistributionBundle](https://github.com/sensiolabs/SensioDistributionBundle), 
a bundle required by default in Symfony Standard Edition.

Don't you think that requirement checks are currently weirdly implemented?
Files are copied from the bundle to the Standard Edition on release. You are
responsible for removing them before deploying (especially `web/config.php`).

The whole logic has been moved to
[symfony/requirements-checker](https://github.com/symfony/requirements-checker).
Want to check requirements? Add the package with Composer. Run the scripts.
Fix any issues. Uninstall the package when done. Files are added and removed
automatically.

The other main feature of SensioDistributionBundle is to execute some commands
when you run `composer install` or `composer update`: they manage the bootstrap
file (which is not needed anymore as of PHP 7), clear the cache, and install
assets. Feels like hardcoded logic (have a look at the
`Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::*` call in your
project's `composer.json` file). I reworked the concept for Symfony 4 in an
extensible way. You can hook into it and add your own commands and logic.

There won't be a SensioDistributionBundle version compatible with Symfony 4. We
don't need one. You can think of Symfony Flex as being the successor of it. A
simpler and more powerful version of it.

Bundle-less Applications
------------------------

When we wrote the first version of the Symfony best practices, we had a long
debate about promoting "one bundle applications" vs "bundle-less applications".
We decided to go with a single `AppBundle` bundle to avoid a too-big
disruption, but also because Symfony was not ready to support bundle-less
applications as a first class citizens. But we worked hard during the last few
months to make sure that Symfony 4 can embrace bundle-less applications.

So Symfony 4 will recommend and generate bundle-less applications. No more
bundle for your code, just use `App\` as a namespace for any class under
`src/`. **It reduces the perceived complexity**. It also makes your code feels
more decoupled from Symfony. I don't think applications should decouple 100% of
its code from the underlying framework, but as of Symfony 3.3, a lot of
features help you just do that.

Bundle-less applications is just one of the best practices changes for Symfony 4.
Best practices is the topic for the next post.
