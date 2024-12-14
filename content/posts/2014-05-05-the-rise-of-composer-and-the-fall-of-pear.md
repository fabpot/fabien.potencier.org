---
date: '2014-05-05'
title: The rise of Composer and the fall of PEAR
summary: |
    The one where I explain my plans about my project PEAR channels.
params:
    author: Fabien Potencier
url: /the-rise-of-composer-and-the-fall-of-pear.html
aliases:
    - /the-rise-of-composer-and-the-fall-of-pear
    - /article/72/the-rise-of-composer-and-the-fall-of-pear
---

A couple of months ago, [Nils Adermann](http://naderman.de/) sent me a nice postcard that reminded me that "3 years ago, we [Nils and me] met for the SymfonyLive hackday in San Francisco." Nils was attending the Symfony conference as he announced the year before that phpBB would move to Symfony at some point.

At that time, I was very interested in package managers as I was looking for the best way to manage Symfony2 bundles. I used PEAR for symfony1 plugins but the code was really messy as PEAR was not built with that use case in mind. The philosophy of [Bundler](http://bundler.io/) from the Ruby community looked great and so I started to look around for other package managers. After a lot of time researching the best tools, I stumbled upon [libzypp](http://en.wikipedia.org/wiki/ZYpp) and I immediately knew that this was the one. Unfortunately, libzypp is a complex library, written in C, and not really useable as is for Symfony needs.

As a good package manager to let user easily install plugin/bundles/MODs was probably also a big concern for phpBB, I talked to Nils about this topic during this 2011 hackday in San Francisco. After sharing my thoughts about libzypp, "..., I [Nils] wrote the first lines of what should become Composer a few months later".

Nils did a great job at converting the C code to PHP code; later on Jordi joined the team and he moved everything to the next level by implementing all the infrastructure needed for such a project.

So, what about PEAR? PEAR served the PHP community for many years, and I think it's time now to make it die.

I've been using PEAR as a package manager since my first PHP project back in 2004. I even wrote a popular PEAR channel server, Pirum (http://pirum.sensiolabs.org/). But today, it's time for me to move on and announce my plan about the PEAR channels I'm managing.

I first [tweeted](https://twitter.com/fabpot/status/433914931130662912) about this topic on February 13th 2014: "I'd like to stop publishing PEAR packages for my projects; #Composer being widespread enough. Any thoughts? #Twig #Swiftmailer #Symfony #php". And on the 14th, I decided to [stop working on Pirum](https://twitter.com/fabpot/status/434256189258735616): "My first step towards PEAR deprecation: As of today, #Pirum is not maintained anymore. http://pirum.sensiolabs.org/ #php"

As people wanted some stats about the [PEAR Symfony channel](http://pear.symfony.com/), I dug into my logs and [figured out](https://twitter.com/fabpot/status/434288993036148736) that most usage came from PHPUnit dependencies: "Stats are clear: my PEAR channels mostly deliver packages related to PHPUnit: Yaml, Console, and Finder. /cc @s_bergmann".

On April 20th 2014, [Sebastian Bergmann](http://sebastian-bergmann.de/) [started the discussion](https://twitter.com/s_bergmann/status/457808573939326976) about PEAR support for PHPUnit: "Do people still install PHPUnit via PEAR? Wondering when I can shut down http://pear.phpunit.de". I immediately [answered](https://twitter.com/fabpot/status/457842648733478913) that: "If @s_bergmann stops publishing PEAR packages, I'm going to do the same for #symfony as packages were mainly useful only for #PHPUnit".

And the day after, Sebastian published [his plan](https://twitter.com/s_bergmann/status/458265980150767616) for deprecating the PHPUnit PEAR channel: "So Long, and Thanks for All the PEARs: https://github.com/sebastianbergmann/phpunit/wiki/End-of-Life-for-PEAR-Installation-Method".

More recently, [Pádraic Brady](http://blog.astrumfutura.com/) also announced the [end of the PEAR channel](https://github.com/padraic/mockery/pull/327) for [Mockery](http://docs.mockery.io/en/latest/).

Besides Symfony, I also manage PEAR channels for Twig, Swiftmailer, and Pirum. So, here is my plan for all the PEAR channels I maintain:

* Update the documentation to make it clear that the PEAR channel is deprecated and that Composer is the preferred way to install PHP packages (already done for all projects);

* Publish a note about the PEAR channel deprecation on the PEAR channel websites (already done for all projects);

* Publish a blog post to announce the deprecation of the PEAR installation mechanism ([Twig](http://blog.twig.sensiolabs.org/post/76626577337/pear-packages-wont-be-published-anymore), [Swiftmailer](http://blog.swiftmailer.org/post/84816320238/pear-packages-wont-be-published-anymore), and [Symfony](http://symfony.com/blog/end-of-pear-support-for-symfony));

* Stop releasing new PEAR packages;

* Remove the PEAR installation mechanism from the official documentation (probably in September this year).

Keep in mind that I'm just talking about stopping publishing **new** packages and promoting Composer as the primary way to install my libraries and projects; the current packages will continue to be installable for the foreseeable future as I don't plan to shut down the PEAR channels websites anytime soon.

On a side note, it's probably a good time to remove PEAR support from PHP itself; and I'm not sure that it would make sense to bundle Composer with PHP.

Happy Composer!
