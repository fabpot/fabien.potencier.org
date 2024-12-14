---
date: '2017-04-03'
title: 'Symfony 4: Compose your Applications'
summary: |
    Symfony 4 is going to be much more than just a cleaned-up version of Symfony 3.
params:
    author: Fabien Potencier
url: /symfony4-compose-applications.html
---

*Note: If you are a Medium user, my new articles are now
[cross-posted](https://medium.com/@fabpot) there as well.*

*Note: Symfony 4.0 will be released at the end of [November
2017](http://symfony.com/roadmap?version=4.0#checker). During the next few
weeks, I will publish articles about my ideas and the main changes I want to
implement for Symfony 4.*

Symfony 3.0 was boring, a cleaned-up version of the Symfony 2.8 version:

> *Symfony 3.0 = Symfony 2.8 - deprecated features*

Symfony 4.0 will be different:

> *Symfony 4.0 = Symfony 3.4 - deprecated features + a new way to develop applications*

There is another way to think about a new major version though:

> *Symfony 4.0 = Symfony 3.0 + all features added in 3.x - deprecated features + a new way to develop applications*

Symfony 4.0 will also require PHP 7.

While thinking about what needs to be changed for Symfony 4, I thought about
the day-to-day experience of managing an application.

Installing a Bundle is too cumbersome
-------------------------------------

The current best way to add more dependencies to a Symfony application is to
install bundles and libraries via Composer packages.

*Fun fact*: Composer started as a conversation about how to generically install
bundles/plugins/extensions for Symfony and phpBB.

*WTF fact*: Neither Symfony nor phpBB uses Composer as a way to install
its bundles/plugins/extensions.

When installing a Symfony bundle, using Composer is not enough; you also need
to enable and configure the bundle correctly before using its features. Here
are the typical steps you need to perform:

 * Execute `composer require symfony/bundle`; it must be manual as developers
   are in charge of selecting the packages they want to install; however, we
   will see that we can make it better when installing dependencies in the
   context of a Symfony application (*hint*: wouldn't it be cool to run
   `composer req cli`, `composer req admin`, or `composer req mailer`?);

 * Register the bundle in the `AppKernel` class;

 * Register some routes (optional);

 * Configure the bundle as you see fit.

The first step cannot be automated, but it feels like the other ones could be.
Configuration cannot be automated but we can probably generate good defaults
like done in the Symfony Standard Edition for some bundles.

Have a look at the documentation of popular bundles. Their READMEs all start
with the same boring dance.

Removing a Bundle is even more cumbersome
-----------------------------------------

From time to time you might want to remove a bundle. How can you do that?
Simple enough: `composer remove symfony/bundle`.

Well, that's not enough.

Remember how we needed to register the bundle, add routes wiring, and
configuration? We need to undo that now, too. This means visiting several files
to remove the bundle. This makes removing a bundle a boring and error-prone
process.

The Symfony Standard Edition is not good enough
-----------------------------------------------

Symfony Distributions was an attempt to fix some of these issues by providing a
good starting point.

The most popular one, the Symfony Standard Edition, is "optimized" for
"traditional" frontend applications for which you need a database, a templating
system, and a way to send emails. But is it still relevant? What if you want to
start a micro-framework style application? Or an API/web service one?

The Symfony Standard Edition makes choices for the developer, but either too
few or too many. **Symfony Distributions do not scale well**; it is not easy to
remove dependencies, and it is not that easy to add new ones.

![Symfony Standard Edition File Structure](/media/articles/2017-04-03-symfony4-compose-applications/symfony-se-files.png)

Another issue with distributions is that they come with files that you don't
want for your project. Like the `LICENSE` and the `README` files. Most projects
are not MIT-licensed and the authors are probably different from the ones who
worked on the distribution. In the same vein, you have to change almost all
entries of the `composer.json` file.

Removing a few files is no problem, but it adds up quickly.

A few years back, the Symfony Standard Edition came with a small demo to get
you started faster. But we removed it as people had a hard time cleaning things
up as the demo spanned many files. It was both a good and a bad decision. It
made the Standard Edition worse as developers don't have any examples anymore,
but it made it also better as developers don't need to clean things up manually
anymore.

Not convinced yet? Have a look at the [REST distribution README file](https://github.com/gimler/symfony-rest-edition#4-getting-started-with-symfony):

![Symfony REST Distribution README](/media/articles/2017-04-03-symfony4-compose-applications/symfony-rest-edition.png)

No Distribution Ecosystem
-------------------------

And that's probably why Symfony Distributions never took off. Apart from the
Standard Edition, not a single one is popular; and as a matter of fact, we only
list three of them on [symfony.com](http://symfony.com/distributions).

Distributions are not flexible enough. You cannot switch from one to another.
You need to buy all their choices or nothing. They cannot evolve.

**Distributions is the wrong abstraction**. We don't need a fully bootstrapped
project. We need a way to grow an application over time.

The ideal Experience
--------------------

As a developer, I want to start small, without too many dependencies. But I
also want to be able to grow my application as I see fit. From a
micro-framework style app to a gigantic monolith. Your choice. The framework
should not get in the way.

Starting a new project with Symfony or evolving a project is currently too
complex for beginners and too cumbersome for advanced Symfony developers. We
can do better.

Composition over Inheritance
----------------------------

If you think about it, distributions use inheritance. Most distributions are
forks of the Symfony Standard Edition with additional bundles. What about using
composition instead? I want to use the API distribution *with* the admin
generator one. Or do we even need distributions? Probably not.

Enter **Symfony Flex**, a new way to create and evolve your applications with
ease.

Symfony Flex is all about making it simple and easy to create any Symfony
applications, from the most simple micro-style project to the more complex ones
with dozens of dependencies. It automates adding and removing bundles. It takes
care of giving you sensible defaults. It helps discovering good bundles.

Symfony Flex is going to be the default way to manage Symfony 4 applications.
But Symfony Flex will be available as an option to manage Symfony 3.3 and 3.4
applications as well! That said, we might need to break backward compatibility
between now and the launch of Symfony 4. Consider Symfony Flex as *alpha*
before Symfony 4.

Stay tuned as the next post will tell you more about Symfony Flex. In the
meantime, have a look at the new [skeleton application used by Symfony
Flex](https://github.com/symfony/skeleton). Not what you would have expected,
right?
