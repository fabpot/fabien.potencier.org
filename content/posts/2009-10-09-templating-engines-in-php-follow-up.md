---
date: '2009-10-09'
title: Templating engines in PHP - Follow-Up
summary: |
    My post about template engines in PHP had now more than 70 comments, and counting. This follow-up tries to answer the most asked questions.
params:
    author: Fabien Potencier
url: /templating-engines-in-php-follow-up.html
aliases:
  - /templating-engines-in-php-follow-up
---

My post about
[template engines in PHP](https://fabien.potencier.org/article/34/templating-engines-in-php)
received more than *70 comments* as of now, and counting.
That's a lot considering most of them are really well thought out, and backed with solid
counter-arguments. Thanks everybody for taking the time to participate
constructively to the discussion. I'm really proud that the PHP community (or
at least the small part which reads my blog) is
able to discuss such a touchy topic without immediately starting a flame war!
I'm also impressed about how many people need to promote their own template
engines ;)

Before I try to answer some questions, I'd like to reinstate that I like PHP
templates. And you should remember that symfony has only used plain old PHP
templates since the beginning. As a matter of fact, I'm been advocating about
using PHP templates since my first PHP project, and I have never used any
other PHP template engines. So, I'm not against PHP templates; I just find
that some PHP limitations as a template language are more and more irritating
for me.

And as
[Eli](http://eliw.wordpress.com/2009/10/07/in-response-to-fabien-potencier-twig-php-templating/)
has pointed out, "[I probably have] grossly undersold PHP-as-template in [my]
enthusiasm to promote [my] new templating language that [I] created".

I also like Tchalvak comment: "The question of PHP vs a templating engine
isn't the essential one, the essential (and easily answerable one) is
templates vs. no templates. Templates - and the separation of display vs.
application logic that they bring with them - are a necessity. What form they
come in is much less important.". He sums up really well the question. More on
that later on.

I understand all the points raised in the comments, and I basically agree with
most of them. Now, let me answer some of the more interesting questions.

Twig Background
---------------

I started to look for a template engine a few months ago. People who know me
also know that I don't like to reinvent the wheel. So, I didn't want to create
a new library from scratch.

I looked for a good template engines, I tried some of them, and finally found
Twig. But as soon as I started to use it, I found that it was exactly what the
gem I was looking for (because of the features it already had and also because
of its clean and beautiful architecture). As Twig was not a standalone
project, but rather an embedded project, I started to hack it a bit, and after
I made several enhancements like the sandboxing feature, I decided to contact
Armin to discuss the future of Twig.

So, you are not sold... yet?
----------------------------

Even if you don't buy my arguments, that's fine. I don't want
[Twig](http://www.twig-project.org/) to become the universal template language
for PHP, far from it. I really think there is a market for template engines
like Twig, but I'm the first to recognize that it won't be used for all PHP
projects to come!

If you are looking for a templating engine that only uses PHP and have
built-in support for template inheritance, blocks, helpers, and some more, the
good news is that I have coded one some weeks ago under the Symfony
[Templating](http://components.symfony-project.org/templating/) Component
name. It's a standalone component, which has no other dependencies, and I'm
sure most people wanting to use plain PHP for their projects will love this
project.

And better yet, you can use both Twig and the Symfony Templating component to
have the best of both worlds. Use the component for all your templates and
Twig if you need the sandboxing feature.

About the Syntax
----------------

Lots of comments about the *syntax*, and the problem I raised about the PHP
syntax. It's not about having a prettier syntax and if you think that the
syntax issues I mentioned are mostly
[bikeshed](http://www.freebsd.org/doc/en/books/faq/misc.html#BIKESHED-PAINTING)
color arguments, think again.

One of the key point of my reasoning is that a good template language should
aim to find the **sweet spot**. The template language should find the best
compromise about too many and not enough features. As I said in my previous
post, the template language is all about *presentation logic*. And of course,
simple conditions and loops are part of the presentation logic. But do you
want to use `array_chunk()` in a template? Probably not. This call belongs to
the controller or the model, depending on what you want to do.

Also, templates are about *a lot* of HTML with *some* PHP. So, this kind of
code snippet is a big no-no for me in a template:


```php
<?php
    if ($items) {
        foreach ($items as $item) {
            echo "* {$item}\n";
        }
    } else {
        echo "No item has been found.\n";
    }
?>

```

Many people seem to like the PHP short tags. First, about the math. If you
compare `<?= $var ?>` with `<?php echo $var ?>`, the difference is **7**
characters, not 2. But that's really not the main problem.

Apart from problems like XML support, the `short_open_tag` setting, the shared
hosts configuration issue, and some more, it's also about coding standards:

 * **PEAR**:

       "Always use `<?php ?>` to delimit PHP code, not the `<? ?>` shorthand. This is
       required for PEAR compliance and is also the most portable way to include PHP
       code on differing operating systems and setups."

 * **Zend**:

       "Short tags are never allowed."

The Pear and Zend projects are serious, so there should have some reasons to
disallow short tags, no?

But as Eli mentioned in his post, I would also like to see a PHP evolution to
take this problem into account: "... there are a number of people (including
myself [Eli]), who have been tossing around the idea of proposing a new option
to the `short_tags` directive for PHP, allowing not just having them turned on
or off. But allowing a 3rd option, that would enable `<?= ?>` while disabling
`<? ?>`"

About Web Designers
-------------------

The debate is not about if web designers should understand PHP or not. And
it's certainly not about web designers not being smart enough. Of course web
designers can learn a bit of PHP... until they learn too much and start
getting stuff that do not belong to the template in their templates (like
getting records directly from the database, anyone?) Oh, of course, they won't
do such a mistake it they have also learned about the MVC pattern. But then,
they are not web designers anymore, they are web developers.

Finding this good balance is all about providing a tool that tries to help web
designers not to shoot themselves in the foot. And I think everybody will be
with me if I say that PHP does a poor job setting the limits.

Output Escaping
---------------

Some commenters advocate that output escaping should be done in the
controllers. That cannot work. As John Campbell writes: "The problem is that
the controller can't possibly be aware of the context of the output, nor
understand which type of escaping is correct."

The issue is therefore not that simple. On the one hand, developers should
take care of output escaping, but it cannot be done in controllers. On the
other hand, web designers should not have to take care about output escaping,
but templates is the only place where you have enough context information to
apply sensible escaping. That's why automatic output escaping seems the best
compromise to me, plus the fact that being "almost" secure by default is a big
advantage.

Speaking of automatic escaping, I'm probably someone who knows a lot about it
as symfony has this feature since early 2006. I can tell you that the
performance overhead of such a feature is very high. In Twig, it is added
during compilation, so there is no overhead whatsoever.

About Sandboxing
----------------

The sandbox feature is not targeted at web designers. It is mainly useful for
situation where external people can change the templates (think of webmasters
able to configure their CMS or blogging platform templates from a backend
interface).

The Dwoo and Smarty security feature is a right step in that direction, but
not as powerful as a full sandbox feature. As far as I understand, it's mainly
about allowing PHP code or not in the templates, and restricting the PHP
functions you can use (of course, correct me if I'm wrong).

Sandboxing controls everything, from the tags you can use, to the methods you
can call on the objects (the Twig
[documentation](http://www.twig-project.org/book/03-Twig-for-Developers)
explains the concept and how it works).

About Speed
-----------

Most template engines compile the templates down to PHP code. So, the speed of
lexing, parsing, and compiling is not that relevant. What matters is the speed
of the evaluation. Here is the compiled version of the `Hello {{ name }}`
template:


```php
/* Hello {{ name }} */
class __TwigTemplate_1121b6f109fe93ebe8c6e22e3712bceb extends Twig_Template
{
  public function display($context)
  {
    $this->env->initRuntime();

    // line 1
    echo "Hello ";
    echo (isset($context['name']) ? $context['name'] : null);
  }
}

```

{{< notice note >}}
Some people were worried about debugging. As you can see, the generated code is
very lean, and contains the template name and the lines of the original
template. That should be more than enough to debug all the possible problems
easily.
{{< /notice >}}

You can find it much more verbose than the PHP version, and of course, for
such a contrived example, the native PHP version is insanely simpler:


```php
Hello <?php echo $name ?>

```

But have a look at the generated code for many template engines and you will
see for yourself that Twig output is one of the cleanest and shortest
one.

As asked by many people, I have redone my benchmark by bypassing the
compilation phase altogether (the cache has been primed before launching the
benchmark scripts). As I expected, it does not change numbers significantly.
The compilation of such simple templates is insignificant compared to the
rendering of 10,000 compiled templates.

I have also tested plain PHP templates. So, here is the updated table:

| Library      | Time (sec) | Memory (Kb)
| ------------ | ---------- | -----------
| Plain PHP    | 2.4        | 114
| Twig         | 3          | 383
| PHPTAL       | 3.8        | 598
| Dwoo         | 6.9        | 1,645
| Smarty 2     | 12.9       | 610*
| Smarty 3     | 14.9       | 799*
| Calypso      | 34.3       | 614
| eZ Templates | 53         | 2,783

* Notice that the memory used by Smarty is much lower than the memory used in
  my previous benchmark as I wrapped the whole loop with an
  `ob_start()/ob_end_clean()`, which was not fair comparing to the other
  template engines. This was fixed in this new benchmark.

So, as expected, PHP is faster than Twig for simple templates. But as
templates become more complex, the difference will be less and less
noticeable, due to the clean code generated by Twig.

{{< notice note >}}
The benchmark scripts are executed on the command line without any PHP
accelerator. A PHP accelerator in this case does not help as everything
is done in one single PHP process.
{{< /notice >}}

You can download the benchmark code here: 
https://fabien.potencier.org/benchmarks.tgz

Twig and Symfony
----------------

This section is for people worried about the integration of Twig as the
default template engine in symfony.

### symfony 1.3/1.4

Obvisouly, Twig won't be part of symfony 1.3 as the feature-freeze deadline is
the end of next week and also because we want the symfony 1.3 release to be an
evolution of symfony 1.2, and not a revolution.

{{< notice note >}}
If some people want to create a Twig plugin for symfony 1.3, start the
discussion on the symfony developer mailing-list, and I will help you
getting started.
{{< /notice >}}

### Symfony 2.0

As far as Symfony 2 is concerned, nothing is definitive yet, but Twig won't be
the default template language. I *think* Twig will be available as an
*optional* plugin, well integrated with the core. For instance, it will
probably make sense to use it for the admin generator to provide some specific
tags that will ease the customization of it.

And thanks to the Symfony
[Templating](http://components.symfony-project.org/templating/) Component,
developers will be able to mix PHP templates and Twig templates easily in one
project, depending on their needs or the tools they want to use.

So, if you can't stand template engines, use plain PHP templates; but if you
want to benefit from Twig, use it natively. And of course, if you use plugins
that made the opposite choice, that won't be a problem either.

Conclusion
----------

Hopefully, this new post has answered some of the points raised in the
comments.



