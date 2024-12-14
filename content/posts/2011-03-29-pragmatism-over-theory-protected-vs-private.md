---
date: '2011-03-29'
title: 'Pragmatism over Theory: Protected vs Private'
summary: |
    I often favor pragmatism over theory; probably because programming is how I
    earn a living but also because I don't have any computer science degree and
    what I know I've learned from my experience.
params:
    author: Fabien Potencier
url: /pragmatism-over-theory-protected-vs-private.html
aliases:
    - /pragmatism-over-theory-protected-vs-private
    - /article/47/pragmatism-over-theory-protected-vs-private
---

I often favor pragmatism over theory; probably because programming is how I
earn a living but also because I don't have any computer science degree and
what I know I've learned from my experience.

In the recent months, there have been a lot of discussions in the PHP
community about the usage of private over protected visibility for methods and
properties in Open-Source libraries.

To summarize my point of view:

**"private" is for purists and "protected" is for pragmatic developers.**

Coming from Perl, I've been a strong advocate of never using private for
anything (as far as I remember, there is not a single use of private in
symfony1; and until recently, the usage of private was explicitly disallowed
in Symfony2 -- it was even part of the Symfony2 coding standards!).

So, why Symfony2 changed its mind? Yep, I've just wrote Symfony2, not I. As
Symfony2 is a community driven project and because we have such a large number
of [contributors](http://symfony.com/contributors), I'm not the only one who
take decisions. And if some people have strong arguments, I'm willing to
change things even if I'm not a big fan of them.

Anyway, a couple of weeks ago, we have started to switch most protected
properties and methods to private. And guess what, just two weeks after the
change, I already have two great stories and lessons to share with you.

Besides obvious mistakes we have fixed right away, all the requests we had for
reverting back to protected lead to better solutions so far.

First, someone complained about not being able to customize the Routing
component. Here is the conversation we had on Twitter (translated from
French):

[@InformatHic](https://twitter.com/#!/informathic/status/51912298478043136):
The closing of the Symfony2 API is a real ordeal. (cc @fabpot)

[@fabpot](https://twitter.com/#!/fabpot/status/51912576862400512):
@InformatHic we can reopen the API if necessary. But give me examples first.

[@InformatHic](https://twitter.com/#!/informathic/status/51957584567091200):
Finally I cleaned up my code and I must say that it is much cleaner now. (cc
@fabpot)

**Having a few well defined extension points force the developer to extend
your library the right way instead of hacking your code.**

And now, for the second example. The Symfony2 Console component is used by
many PHP libraries outside the Symfony world, like [Behat](http://behat.org/).
For Behat, [Konstantin](https://twitter.com/everzet) used to override some
methods that I've switched from protected to private. At first, he was
annoyed. But after some discussion with
[Bulat](https:/twitter.com/avalanche123), they came up with a much better
[solution](https://github.com/symfony/symfony/pull/312): they actually
decoupled the code and added a new and clean way to solve the problem at hand
and as a result, they made the component more beautiful and more customizable.

**Closing the API allows design flaws to be found more easily and gives you
the opportunity to evolve your code by creating well defined extension
points.**

So, even if I still believe that using private is not a hard rule you should
blindly follow, I can see some pragmatic advantages. And it's not like we
cannot change our mind if needed. Changing a private method to protected is
simple and won't break any code. But before doing that, you need to understand
what the developer is trying to achieve and if you cannot find a better way to
resolve his problem without opening the API too much.

By the way, public methods does not define the public API. Sometimes, you
need to declare a method as public because you need to access it from another
class in your library; but that does not mean that you want your users to
actually call it directly. What do you do then? Symfony and Flow3 have decided
to mark the "real" public API with the `@api` tag. All classes, methods, and
properties tagged with `@api` are public in the sense that we guarantee their
*stability over time*: their name, signature, and behavior won't change for
any minor version of the library. That's a strong commitment; one that I like
much better than any strict enforcement done on the language level.



