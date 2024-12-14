---
date: '2014-11-06'
title: About Personal Github Accounts
summary: |
    I'm moving most of my projects to Github organizations.
params:
    author: Fabien Potencier
url: /about-personal-github-accounts.html
aliases:
    - /about-personal-github-accounts
    - /article/75/about-personal-github-accounts
---

Many of you have a user account on Github. But what are you using it for? As
far as Open-Source is concerned, I'm using [mine](https://github.com/fabpot)
for two different usages:

 * as a way to contribute to other projects by forking repositories and making
   pull-requests;

 * as a way to host some of my Open-Source projects.

But the more I think about it, the more I think the second usage is most the
time wrong. If you are publishing a small snippet of code, a small demo, the
code for a tutorial you wrote on your blog, that makes a lot of sense. But when
it comes to useful and/or popular Open-Source projects, I think that is a
mistake.

An Open-Source project should not be tied too much to its creator; the creator
just happens to be the first contributor. And for many projects, it will stay
that way for a very long time, which is fine. But gradually, as more people
contribute, it can confuse some users. The license you choose helps a lot and
the way you are responding to pull requests and issues is also a great way to
show you openness. But that's not enough in the long term. Of course,
understanding when it becomes a problem is up to you and definitely not easy.
Here are some of my thoughts about some problems I identified in the past.

First, it makes the original developer special and not aligned with how others
contribute; you cannot for instance fork the project to make a pull request
(with a not-so-nice side-effect of Packagist publishing your branches, which is
obviously wrong.)

Then, bringing awareness through a well established organization is probably
easier than promoting yourself; it makes your project more easily discoverable.

Also, what if someone starts to contribute more than you? What if you are not
interested in maintaining the project anymore? Github makes it very easy to
transfer a project to another person, but organizations are almost always a
better way in that case.

And I'm not even talking the [bus factor](http://en.wikipedia.org/wiki/Bus_factor).

As you might have guessed by now, Github organizations is the solution. A
organization fixes all the problems and then some more; and creating one is
very easy. Again, that only makes sense when your project is somewhat
successful, and probably even more interesting if you have more than one such
projects.

A while ago, I decided to do that for
[Silex](https://github.com/silexphp/Silex) and I moved it to its own
[organization](https://github.com/silexphp). And I did the same for
[Twig](https://github.com/twigphp/Twig) recently for the same reasons. For those projects, it
made sense to create a dedicated organization because there is more than one
repositories; we moved along some related repositories (like the Silex skeleton
or the Twig extensions).

Organizations are also a great way to create a group of people working on
related topics (like [FriendsOfSymfony](https://github.com/FriendsOfSymfony)) or
people working with the same standards
([The League of Extraordinary Packages](https://github.com/thephpleague)).

Last year, I co-created such an organization:
[FriendsOfPhp](https://github.com/FriendsOfPhp). A couple of weeks ago, I moved
the PHP security advisories database from the [sensiolabs](https://github.com/sensiolabs) organization to the
[FriendsOfPhp](https://github.com/FriendsOfPhp/security-advisories) one
and I explained my [motivations](http://fabien.potencier.org/article/74/the-php-security-advisories-database) in a blog post.

Today, I'm doing the same with several of my projects that were previously part
of my personal Github account. I have not created an organization per project
because they are either too small or they don't need more than one repository;
so they would not benefit from a standalone organization.

 * [Sismo](https://github.com/FriendsOfPhp/Sismo): A Continuous Testing Server
 * [Sami](https://github.com/FriendsOfPhp/Sami): An API documentation generator
 * [PHP-CS-Fixer](https://github.com/FriendsOfPhp/PHP-CS-Fixer): A script that fixes Coding Standards
 * [Goutte](https://github.com/FriendsOfPhp/Goutte): A simple PHP Web Scraper

If you cloned one of these repositories in the past, you can easily switch to
the new Git URL via the following command:

    $ git remote set-url origin https://github.com/FriendsOfPhp/XXX.git



