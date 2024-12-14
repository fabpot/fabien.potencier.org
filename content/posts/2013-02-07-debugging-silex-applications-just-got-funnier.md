---
date: '2013-02-07'
title: Debugging Silex applications just got fun
summary: |
    Enjoy using the Symfony Web Profiler into your Silex application.
params:
    author: Fabien Potencier
url: /debugging-silex-applications-just-got-funnier.html
aliases:
    - /debugging-silex-applications-just-got-funnier
    - /article/66/debugging-silex-applications-just-got-funnier
---

What is the difference between a full-stack framework and a micro framework?
To me, this is not about the amount of code, but more about how you build an
application and how you structure it. Symfony and Silex share a lot of code
(Silex specific code being quite minimal), but developing an application with
one or the other is a totally different experience. And choosing between the
two really depends on your needs, the application you want to create, the
skills of your team, etc...

One of the big advantages of both Symfony and Silex is that they are based on
the Symfony
[HttpKernel](http://symfony.com/doc/master/components/http_kernel/introduction.html)
component. Why is it an advantage? One of the reasons is the standardization
of the HTTP dialog between the client and the application server and the
standardization of the HTTP messages via the
[HttpFoundation](http://symfony.com/doc/master/components/http_foundation/introduction.html)
component; that makes all applications using it easily interoperable (like
Drupal, eZPublish, Laravel, and many others).

But handling HTTP requests with the Symfony HttpKernel also gives you a lot of
free and optional features like a built-in reverse proxy written in PHP, easy
handling of ESI and Hincludes (via the Fragment sub-framework), or the
gorgeous Symfony web profiler:

![The timeline panel in the Symfony Web Profiler](http://symfony.com/uploads/assets/profiler_timeline.jpg)

I've just published a [Silex provider](https://github.com/sensiolabs/Silex-WebProfiler) that
integrates the Symfony web profiler into Silex.

To get started faster, create a new application via my [Silex Skeleton](https://github.com/fabpot/Silex-Skeleton), where the
profiler is already integrated and available when in the development
environment:

    composer create-project fabpot/silex-skeleton somewhere/ 1.0.x-dev

Igor recently wrote about [HttpKernel](https://igor.io/2013/02/02/http-kernel-middlewares.html)
and [HttpFoundation](https://igor.io/2013/02/03/http-foundation-value.html)
on his blog... interesting read.



