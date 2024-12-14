---
date: '2012-07-04'
title: PHP is much better than you think
summary: |
    Rants about PHP are everywhere but do you really know what PHP can do for you nowadays?
params:
    author: Fabien Potencier
url: /php-is-much-better-than-you-think.html
aliases:
    - /php-is-much-better-than-you-think
    - /article/64/php-is-much-better-than-you-think
---

Rants about PHP are everywhere, and they even come from smart guys. When Jeff
Atwood wrote yet another
[rant](http://www.codinghorror.com/blog/2012/06/the-php-singularity.html)
about PHP, it made me think about the good parts of PHP.

The biggest problem of these rants is that they come from people stuck in the
old days of PHP. They either don't care or they don't want to admit that PHP
actually evolves at a very fast pace, both at the language level but also at
the community level. In fact, it evolves much faster than any other language
or web platform. It has not always been the case, but the last 5 years have
been an amazing journey for PHP.

Before talking about the amazing things the PHP community has achieved
recently, let's have a look at some interesting numbers: PHP is used by
[77.9%](http://w3techs.com/technologies/overview/programming_language/all) of
all the websites whose server-side programming language is known. WordPress is
used by
[16.6%](http://w3techs.com/technologies/overview/content_management/all) of
**all the websites** in the world. If you have a look at the top three CMSes,
for the websites that use a monitored content management system: Wordpress is
first with 54.3%, Joomla is second with 9.2%, and Drupal is third with 6.8%.
Three products written in PHP.

PHP must have done something right, no?

Now, let me tell you a secret, the PHP "tour de force": Despite the changes
over the years, PHP is still the **easiest language** to learn for
non-technical people: it allows anyone to create dynamic websites faster than
with any other technologies, it allows anyone to host websites cheaply and
without any hassles. PHP is probably not the best designed language in the
world, but it lets you **get things done**, and you can't argue with that.

PHP, the Language
-----------------

PHP 5.0 (released in 2004) brought us a very solid object model... wait a
minute, I'm talking about something released almost 8 years ago. Fast forward
now. The latest PHP release, PHP 5.4, comes with all the bells and whistles
you might dream of in a modern web language: yes, PHP supports namespaces;
yes, PHP supports closures; yes, PHP supports traits.

It took some time, but PHP 5.4 also comes with some nice syntactic sugar that
makes the whole experience better than ever: yes, PHP supports `[]` to define
arrays; yes, PHP supports calling a method on a newly created object (`(new
Foo())->bar()`); yes, PHP supports getting an array item from any expression
(`$foo->bar()[1]`).

PHP has even learned from its mistakes: `register_globals` and `magic_quotes`
are definitely gone.

Last, but not the least, PHP even comes with a built-in web server that eases
local testing... and it starts in a matter of micro-seconds.

*Next challenges*: How do we "upgrade" all the old tutorials talking about PHP
on the web? What is the best way to support the WebSocket technology in a PHP
application?

PHP, the Ecosystem
------------------

Having a good language is great, but having a great ecosystem is even better.
And the PHP ecosystem has evolved a lot in the last few years.

### Git

I won't talk too much about this one. Git is everywhere and PHP embraced Git
pretty fast. Almost all major PHP libraries, frameworks, and products are now
using Git, including PHP itself.

### Composer

Two years ago, I wanted to get rid of my ugly-PEAR-hack I did in symfony 1 to
support plugins. I wanted to replace it with something that was able to manage
project dependencies, not a global installer like PEAR. Managing dependencies
is not an easy task, so I tried to find the best algorithm to manage software
dependencies; I had a look at everything: from Perl to Ruby, from Debian to
Redhat. Nothing was satisfactory: only homegrown solutions that happen to
work... empirically. Then, I stumbled upon
[ZYpp](http://en.wikipedia.org/wiki/ZYpp). That was it. ZYpp uses a
[SAT](http://en.wikipedia.org/wiki/Boolean_satisfiability_problem) solver to
manage dependencies. Fast forward. Thanks to the hard work of
[Nils](http://www.naderman.de/) Adermann and [Jordi](http://seld.be/)
Boggiano, PHP now has one of the best dependency manager,
[Composer](http://getcomposer.org/).

Yes, PHP has a better dependency manager than any other languages.

And thanks to Git, Composer, and the PHP built-in web server, it has never
been [easier](https://twitter.com/fabpot/status/220254879459975169) to
download/install/test a PHP project.

Want to test Symfony (using PHP 5.4)?

    $ composer.phar create-project symfony/framework-standard-edition
    $ cd framework-standard-edition
    $ ./app/console server:run

Want to test Silex?

    $ composer.phar create-project fabpot/silex-skeleton
    $ cd silex-skeleton
    $ php -S localhost:8888 -t web/

Don't know Composer yet? You should learn about it. Browse
[Packagist](http://packagist.org/packages/), the main Composer repository: it
already has 1900+ packages available and they have been installed more than a million times
in less than 3 months.

*Next challenge*: include the Composer installer in the next PHP version?

### Collaboration

Community collaboration is the most important point of this post; the one I'm
the most proud of. We start to see better collaboration between PHP projects,
even from the very big ones, the ones you would think are large enough to not
care about the others.

phpBB, [Drupal](http://symfony.com/blog/symfony2-meets-drupal-8), [eZ Publish](http://symfony.com/blog/symfony2-meets-ez-publish-5), Symfony, and many others (phpDocumentor, PHPUnit,
Behat, Zikula, Propel, Doctrine, Midgard, ...) are **sharing** code. Yes, they
are "competitors" but they all understood that cross-pollination was a good
thing. And Composer is a good enabler.

*Next challenge*: Convince even more projects to join the trend.

Conclusion
----------

Let me say it again: PHP is probably not the best language out there, and I'm
the first one to scream about its quirks, but **PHP is the best web
platform**... ever.


