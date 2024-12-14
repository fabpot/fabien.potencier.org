---
date: '2012-09-07'
title: Why Symfony?
summary: |
    Choosing a PHP framework is not easy. Why would you choose Symfony over framework X?
params:
    author: Fabien Potencier
url: /why-symfony.html
aliases:
  - /why-symfony
  - /article/65/why-symfony
---

Yesterday, Zend Framework 2.0 and Symfony 2.1 were released... almost at the
same time. First, I want to congratulate the Zend Framework team for this huge
milestone; I know that working on a new major version is no small task.

And of course, people started to ask questions about these new versions and
one of the most popular was: "Why would I choose framework X over framework
Y?". As you can imagine, the answer is not that easy as each framework has its
own specificities.

I like to think that most popular frameworks are modular enough, fast enough,
well-documented enough; they are using well-known design patterns. So, besides
the buzzwords, what is unique to Symfony? Why would you want to use Symfony
instead of X (replace X with ZF, CakePHP, Lithium, Laravel, Slim, Yii, you name it)? That's the question I'm going to answer in this post.

So, without further ado, here are my main selling points for Symfony:

 * Symfony is not a framework but a project. Depending on your needs, you can
   choose to use some of the [Symfony
   Components](http://symfony.com/components), the
   [Silex](http://silex.sensiolabs.org/) micro-framework, or the full-stack
   framework.

 * Symfony is used by many large companies (like the BBC or CBS), by many large websites (like [TED](http://www.ted.com/), [wetter.com](http://wetter.com/), [Lockers](http://lockerz.com/) or even YouPorn just to name a few) and some Open-Source projects are
   also powered by Symfony (CMSes like
   [Drupal](http://symfony.com/blog/symfony2-meets-drupal-8) or
   [eZpublish](http://symfony.com/blog/symfony2-meets-ez-publish-5), libraries
   like [PHPUnit](https://github.com/sebastianbergmann/phpunit/) or
   [Doctrine](http://www.doctrine-project.org/), products like
   [phpBB](https://www.phpbb.com/) or [shopware](http://www.shopware.de/), and even frameworks like
   [PPI](http://www.ppi.io/) or [Laravel](http://laravel.com/)). This brings a lot of interoperability between all these solutions.

 * Symfony enjoys a huge [community](http://connect.sensiolabs.com/) of users
   and [contributors](http://symfony.com/contributors); during the last year
   alone, 550+ people contributed to the Symfony core and the community
   created over 1,600 [bundles](http://knpbundles.com/) for the full-stack
   framework. Symfony also has several annual dedicated
   [conferences](http://live.symfony.com/) around the world and a large number
   of user [groups](http://symfony.com/events).

 * Symfony has been created in 2005 and here to stay. Besides
   [SensioLabs](http://sensiolabs.com/), many other companies rely on Symfony
   for their clients and they contribute, invest money, and sponsor the future
   of the project.

 * Symfony embraces the "don't reinvent the wheel" philosophy, and provides
   tight integration with many other Open-Source projects (like Monolog,
   Assetic, Doctrine, Propel, ...).

 * Symfony tries to bring innovation to PHP: it was one of the first major
   frameworks to embrace PHP 5.3, to introduce the usage of a Dependency
   Injection container, and to use a templating engine for its templates by
   default, [Twig](http://twig.sensiolabs.org/), which is now also adopted by
   major CMSes like Drupal and eZpublish. Symfony also has some unique
   features like its gorgeous debug toolbar and its great built-in profiler.

If you think I've missed some important selling points, feel free to list them
in the comments and in a few days, I will aggregate everything in a new page
that will be hosted on symfony.com to help users make an informed decision
when choosing a framework.

And if other frameworks would like to do the same, I would happily add links
to their pages on the Symfony one, and if in turn, they do the same, that
would create a ring of links that will ease the choice of a PHP framework for
developers.


