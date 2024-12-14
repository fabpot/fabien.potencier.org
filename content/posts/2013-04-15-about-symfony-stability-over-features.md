---
date: '2013-04-15'
title: 'About Symfony: Stability over Features'
summary: |
    Everybody knows about "Convention over Configuration" introduced by Rails. What about "Stability over Features" for Symfony?
params:
    author: Fabien Potencier
url: /about-symfony-stability-over-features.html
aliases:
  - /about-symfony-stability-over-features
  - /article/68/about-symfony-stability-over-features
---

{{< notice warning >}}
I know that this post won't please everyone, but I'm convinced that this
is the right thing to do now and I think that most of the Symfony
community will love it. Also, I've tried to be subtle in choosing my
words, so read carefully and don't over-read what I've written.
{{< /notice >}}

**Long story short**: in the coming months, the Symfony core contributors
should focus their efforts toward stabilizing the existing features instead of
working on new ones. At this point, backward compatibility and stability are
more important than everything else.

Symfony is one of the few PHP projects that has been promoting radical changes
to the way developers work with PHP by leveraging many concepts, best
practices, and design patterns from other languages and technologies. But
Symfony is also one of the few to embrace true collaboration and sharing. That
was not necessarily incompatible in the "early days" of Symfony2, but to
continue doing both the right way, the community needs to slightly change its
priorities.

To start easy, let's see what **stabilization** means for me:

 * **Refactor less**: We need to stop doing refactoring code for the sake of
   it. Sometimes, our code is not the best architected one, sometimes we could
   simplify some code, or make it easier to read, but if it does not make
   things easier for our users, we should avoid doing refactoring.

 * **Fix more bugs and edge cases**: Symfony has twenty-plus components, small
   and large, and we are commited to maintain them in the long term. And
   working on fixing bugs that people encounter or fixing edge cases is a
   must. Most developers prefer to work on new features rather than fixing
   bugs, but personnally, I do like fixing bugs because most of time fixing a
   bug is a challenge... and we like challenges, right?

 * **Write more tests**: Symfony2 has many unit tests but we need more. We
   need to write tests that cover bugs and edge cases that we fix, but some
   parts of the framework are not well tested enough.

 * **Write more documentation**: Thanks to a great doc team, Symfony2
   documentation is really good and it is updated and improved every single
   day. We should continue this work and the more people helping, the better.
   The good news is that everyone is able to contribute here.

 * **Stop breaking backward compatibility**: This is the most important topic
   and I know that many core contributors are nervous about this one. When we
   break backward compatibility, we are breaking the Symfony2 ecosystem at
   large. So, except when fixing a security issue, we must not break backward
   compatibility anymore after Symfony 2.3 and until Symfony 3.0.

Let's take some examples to make things clear.

What about fixing a wrong or stupid behavior on an existing feature? As we
cannot break backward compatibility, and because people might rely on the
current behavior, we should add a way to do the feature the right way instead
and thoroughly document both behaviors by emphasizing the right way and by
deprecating the wrong way until Symfony 3.0.

What about renaming a miss-named thing? We cannot rename the
method/class/function/namespace/whatever, but we can add an alias and
deprecate the wrong one, which will be removed in Symfony 3.0.

What about a new feature then? Of course, we will still add new features. If
it does not impact the stability of the framework, that's fine: adding the new
table helper for the console is a great example for instance, or supporting
PIDs in the process component. But I believe that we already have a solid set
of features that can be used to solve many complex web problems. And most of
the time, for features that we don't have, there is already a good PHP
librairies that we should leverage instead.

What about adding a new component? Again, that's still possible either by
extracting code from other components (like we did for the new Debug component
in 2.3 -- and without breaking backward compatibility), or by adding new ones,
but that should not be our priority for the coming months.

Stability is also a **great enabler**:

 * **Enhance performance**: I'm not talking about micro-optimizations here,
   but performance on real projects. It was not really practical until now
   because Symfony2 was a moving target, but stabilization will enable us to
   make performance test beds and benchmarks to ensure that performance
   improves with new releases.

 * **Convert more projects**: I'm really happy that so many Open-Source
   projects rely on Symfony one way or another, but we need to convert more.
   By promoting sharing, we also promote reusability and compatibility between
   many different PHP projects, and that's a very good thing to do.

 * **Convert more "corporate" users**: When working in big companies, you need
   to plan your work ahead of time and for many years. So, you can only work
   with librairies that are maintained and stable in the long term.

I also want to point out that I've tested this approach with Twig. Twig
version 1.12.3, released last week, is enterily backward compatible with Twig
1.0, released two years ago (to be fair, we made one mistake in 1.2 if I
remember correctly). Of course, Twig is much smaller than Symfony, but if you
take each Symfony component as an individual library, then the same approach
can be applied. Last year, I started to work on Twig 2.0 because I thought
that introducing some great features was not possible with the current
architecture, but with some more work, I did introduce everything I wanted in
Twig 1 without breaking backward compatibility. That's a big win. And that's
probably one of the reasons why so many projects plan to use Twig nowadays
(Drupal, eZPublish, Magento2, ...): **Twig evolves fast but in a stable
way**. By the way, that's why namespaces won't be introduced in Twig anytime
soon: we don't have a need for them.

At this point, I should probably answer one question: when will Symfony3 be
released? As you might have guessed now, there is no plan or schedule for
Symfony3; so it's not going to be released anytime soon. We do have an UPGRADE
file for Symfony3, but only because we need to reference deprecated features
that will be supported for all 2.x versions but that will be removed for
Symfony3. And these deprecated features won't trigger any deprecation messages
until we have a plan for Symfony3. So, you can upgrade to the new way, but not
doing so won't affect you at all.

I hope that this strategy won't slow down the number of contributions. I
believe that it will ease the adoption of Symfony2 and I believe that it won't
stop us from innovating. And by making a stronger contract with our users, we
are opening great opportunities for the Symfony ecosystem: bundles, libraries,
projects, frameworks, CMSes, e-commerce solutions, ...

To conclude, think of Symfony as being **a PHP middleware** instead of a PHP
framework and everything should click. This is just the beginning of the
Symfony story, let's make it strong and awesome together.



