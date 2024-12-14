---
date: '2013-06-17'
title: Packing a Symfony full-stack Framework Application in one File -- Introduction
summary: |
    This is the first part of a series of articles about packing a Symfony full-stack application in one file. This one explains why this might come in handy and why the challenge itself is interesting.
params:
    author: Fabien Potencier
url: /packing-a-symfony-full-stack-framework-application-in-one-file-introduction.html
aliases:
    - /packing-a-symfony-full-stack-framework-application-in-one-file-introduction
    - /article/69/packing-a-symfony-full-stack-framework-application-in-one-file-introduction
---

{{< notice note >}}
This article is part of a series of articles that explains how to pack a Symfony
full-stack application in one file. The first article explains why this might actually
be useful:
1) [Introduction](https://fabien.potencier.org/article/69/packing-a-symfony-full-stack-framework-application-in-one-file-introduction),
2) [Bootstrapping](https://fabien.potencier.org/article/70/packing-a-symfony-full-stack-framework-application-in-one-file-bootstrapping),
...
{{< /notice >}}

Sometimes, I'm wondering if I'm not just completely crazy. I like small
things, but I'm the author of Symfony, a not-so-small framework (about eighty
thousand lines of code excluding comments as of today). And that's probably
because I like to push the limits of what's possible when coding.

In 1985, my first useful piece of code was about managing a portfolio of
stocks for my parents. I was twelve years old at that time. I won a contest.
It was an interesting challenge: coding a full game in less than [10 lines of
code](https://fabien.potencier.org/article/32/developers-should-be-artists).
And my code was published in a French magazine.

In 2009, I tweeted an implementation of a [dependency injection
container](http://twittee.org/) in less than 140 characters (I did the same
with a [web framework](http://twitto.org/)).

And most of the time, those experiments helped me get to the next level.
Twittee, my service container that fits in a Tweet, was an experiment, but
then, it became [Pimple](http://pimple.sensiolabs.org/), a small dependency
injection container that is used today in
[Silex](http://silex.sensiolabs.org/), a micro-framework based on the Symfony
components.

So, that's not about just trying to push the limits, or trying to have fun.
It's also about **experimenting different approaches to known problems** and
see if they can have practical usage.

So, 2013... time for another challenge, right? What about packing a Symfony
full-stack application in a single file. No Silex, no phar allowed, no
compilation phase, just everything in a single readable file: from assets to
controllers, from templates to Composer configuration.

Why?
----

This is yet another step toward my Quest of the PHP Holy Grail. But besides
being a though challenge, there are many other reasons that makes it
interesting for everyone.

First, that's a good way to learn more about the Symfony internals and
especially about the [Kernel](http://api.symfony.com/2.3/index.html) class.
Nowadays, thanks to all the talks about HttpKernel given by various speakers
at various conferences, and thanks to my [series of
articles](https://fabien.potencier.org/article/50/create-your-own-framework-on-top-of-the-symfony2-components-part-1)
about it on my blog, a lot of developers understand how Symfony handles
requests and how it manages the conversion to responses. There is even a full
[chapter](http://symfony.com/doc/current/components/http_kernel/introduction.html)
about it in the official documentation.

But the Symfony Kernel is less well-known. This is a shame as it is also a
very interesting piece of software. I hope that this challenge will give you
more information about the Symfony Kernel and that, as a result, more
Open-Source projects will adopt it instead of just using the components.

Then, I want to showcase once more the flexibility of the Symfony core
framework and the decoupling between all aspects of the framework. If you are
just a Symfony developer, you might not realize how the low level architecture
of Symfony works, and I'm going to give you some insights about it.

Also, there is a more practical usage: **bug reporting**. When you report a
Symfony bug, sometimes, it is not that easy to reproduce it. Probably because
it involves third-party bundles, a specific configuration, or a chain of
controller calls. For such bugs, it is almost impossible to make a patch
without a way to reproduce it. As a matter of fact, we often ask reporters to
fork the Symfony Standard Edition and modify it in a way that exhibits the
issue. But doing so is tedious for both the reporter and the developer that
will try to fix the bug. Doing the same with Silex is more easier as most of
the time, the reporter is able to package everything is a single file. So,
being able to do the same with the full-stack framework would be a huge step
forward.

Reporting bugs is fine, but being able to experiment things in a small
environment also helps. A few weeks ago, Jordi submitted [a new API
layer](http://symfony.com/blog/new-in-symfony-2-4-customize-the-security-features-with-ease)
to simplify the configuration of Symfony's Security. To better understand how
to use it and to get a feeling for the new API, I decided to create a small
project to test different common configurations. And being able to do that in
one file would have helped me a lot.

Being able to pack a whole application in one file is also very useful when
you are teaching Symfony to someone, or if you want to show how to use a
specific Symfony feature.

Micro vs Full-Stack
-------------------

So, let's sum up things a bit. I created Twitto, a micro-framework that fits
in a tweet. Then, Silex, a micro-framework based on the Symfony components.
And now, I want to make it possible to create a full-stack Symfony application
in just one file. Does it mean that Symfony full-stack is also a
micro-framework?

What is a micro-framework? Micro means "extremely small" and a framework is a
set of tools that structures an application. It abstracts the low-level
architecture of a project.

To me, **micro does not mean less code**. The user of a framework does not
care about the number of lines of code in the framework. It does not matter
when using the framework.

To me, **micro means less structure, less constraints, less decisions made for
you**. Full-stack frameworks are great as they provide a sensible default
directory structure; they come with many integrated features; they allow you
to get started faster; they allow you to easily understand what's going on on
an existing project.

A micro-framework is just about the bare minimum you need to get started. It
lets you decide which directory structure you want, it lets you decide which
feature you want to enable, it lets decide how you want to work.

Two different approaches to solve the same problem. And both have valid use
cases. So, comparing a micro-framework to a full-stack one should not be based
on the number of lines of code, but the comparison should be done on the way
you are using the framework.

The Challenge
-------------

So, is it possible to pack a Symfony full-stack application in one file? And,
is it possible to do that in less than 200 lines of code? Well, take a seat,
fasten your seatbelt, grab a coffee, and relax. I'm going to show you how I
took up the challenge in this series of articles, and along the way, I'm going
to explain how Symfony works. Hopefully, you're also going to learn a few
tricks about PHP and Symfony.

That's all for the philosophy of the challenge. Next time, we will start
tweaking the Symfony Standard Edition.



