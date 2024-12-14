---
date: '2009-10-07'
title: Templating Engines in PHP
summary: |
    So, you think PHP is a templating engine? So did I... for a very long time.
    But recently, I changed my mind. Here is why.
params:
    author: Fabien Potencier
url: /templating-engines-in-php.html
aliases:
  - /templating-engines-in-php
  - /article/34/templating-engines-in-php
  - /article/35/templating-engines-in-php-follow-up
---

{{< notice warning >}}
This blog post is not for the faint-hearted! Some people will strongly
disagree with me and some others will probably want to kill me at the
upcoming [Zend Conference](http://www.zendcon.com/). And
if starting an argument in the comments can help you feel better,
please feel free to do so. If you want to have a more advanced discussion
on this topic, vote for my talk at the Zend
[UnConference](http://joind.in/talk/view/959).
{{< /notice >}}

So, you think PHP is a templating engine? So did I... for a very long time.
But recently, I changed my mind. Even if PHP can be used as a templating
engine, the syntax is just plain ugly as a template language.

For several years now, I have been promoting web development best practices,
and one of them is the separation of concerns. Of course, as the lead
developer of symfony, all the projects we work on at Sensio are modeled after
the MVC architecture. It certainly helps when we have big projects where many
people need to work together. The developers work on the code (the Controllers
and the Model) and the web designers work on the design. And templates are
sometimes written by developers, but more often than not, they need to be
written by web designers or by the webmasters themselves.

And a template language is something that helps you to write templates that
respects this separation of concerns. A template language should find a good
balance between giving enough features to ease implementing the *presentation
logic*, and restricting the advanced features to avoid the *business logic* to
cripple your templates.

So, when I asked a few days ago about the best and popular templating engines
in PHP on [Twitter](http://twitter.com/fabpot/status/4632858523), some people
naturally answered "PHP" itself. I was not even surprised as that would
probably have been my answer some weeks ago too.

Why PHP is not (anymore) a good template language?
--------------------------------------------------

Why do people still think PHP is a templating engine? Sure enough, PHP started
its life as a template language, but it did not evolve like one in the recent
years. If you think PHP is still a template language, can you give me just one
recent change in the PHP language which enhanced PHP as a template language? I
cannot think of one.

Template languages evolved a lot since 1995 and the initial release of
[PHP/FI](http://fr2.php.net/manual/en/history.php.php):


```html
&lt;!--include /text/header.html--&gt;

&lt;!--getenv HTTP_USER_AGENT--&gt;
&lt;!--ifsubstr $exec_result Mozilla--&gt;
  Hey, you are using Netscape!&lt;p&gt;
&lt;!--endif--&gt;

&lt;!--sql database select * from table where user='$username'--&gt;
&lt;!--ifless $numentries 1--&gt;
  Sorry, that record does not exist&lt;p&gt;
&lt;!--endif exit--&gt;
  Welcome &lt;!--$user--&gt;!&lt;p&gt;
  You have &lt;!--$index:0--&gt; credits left in your account.&lt;p&gt;

&lt;!--include /text/footer.html--&gt;

```

And as a matter of fact, *PHP doesn't support many features modern template
languages should have nowadays*.

{{< notice note >}}
I will take Django as an example of a modern template language in my examples
for reasons you will understand later on, and mainly because I think Django
template language hits that sweet spot I talked about above.
{{< /notice >}}

The following sections describes the main features I want to find in a modern
template language:

### Concision

The PHP language is verbose. You need no less than 14 characters just to
output a simple variable (and no, using the more compact `&lt;?=` shortcut is not
an option):


```php
&lt;?php echo $var ?&gt;

```

And PHP becomes ridiculously verbose when it comes to output escaping (and
yes, escaping variables coming from an unsafe source is mandatory nowadays):


```php
&lt;?php echo htmlspecialchars($var, ENT_QUOTES, 'UTF-8') ?&gt;

```

Compare with the same examples written with the Django template language:


```html
{{ var }}

{{ var|escape }}

```

### Template oriented syntax

This one is mostly a matter of taste, but modern template language have nice
idioms to express common needs. For instance, let's say you want to iterate
over an array and want to display a default text when the array is empty.
That's very common, but the PHP version is not very readable:


```php
&lt;?php if ($items): ?&gt;
  &lt;?php foreach ($items as $item): ?&gt;
    * &lt;?php echo $item ?&gt;
  &lt;?php endforeach; ?&gt;
&lt;?php else: ?&gt;
    No item has been found.
&lt;?php endif; ?&gt;

```

The Django version is much better thanks to the use of an `else` clause for
the `for` tag:


```php
{% for item in items %}
  * {{ item }}
{% else %}
  No item has been found.
{% endfor %}

```

### Reusability

PHP has greatly evolved over the years as far as reusability is concerned.
Since PHP5, the object implementation is much better, and when
[traits](http://wiki.php.net/rfc/horizontalreuse) will be supported in the
next version of the language, we will have a solid general purpose language at
our disposal. I'm really happy with all these changes as it helps me write
better programs, but these enhancements are irrelevant when what you want to
do is to mainly write templates.

Django introduced template
[inheritance](http://docs.djangoproject.com/en/dev/topics/templates/#id1) some
years ago, as a way to mimic classes but for templates:


```html
&lt;!-- base.html --&gt;
&lt;html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en"&gt;
  &lt;head&gt;
    {% block head %}
      &lt;link rel="stylesheet" href="main.css" /&gt;
    {% endblock %}
  &lt;/head&gt;
  &lt;body&gt;
    {% block content %}{% endblock %}
  &lt;/body&gt;
&lt;/html&gt;

&lt;!-- index.html --&gt;
{% extends "base.html" %}

{% block head %}
  {{ block.super }}
  &lt;link rel="stylesheet" href="main.css" /&gt;
{% endblock %}

{% block content %}
  Index content
{% endblock %}

```

It's elegant, easy to understand, and really powerful. It's so powerful that
many template engines now support this feature out of the box.

### Security

I'm not saying PHP is not a secure language, far from it. But needless to say
that escaping a variable in a template is just a nightmare as I have showed
you previously:


```php
&lt;?php echo htmlspecialchars($var, ENT_QUOTES, 'UTF-8') ?&gt;

```

Of course, you can create your own function to make it shorter, but that's not
my point:


```php
&lt;?php echo e($var) ?&gt;

```

For me, security should be enabled by default, especially for templates
written by non-developers who are not necessarily aware of the common web
threats like XSS or CSRF.

As far as I know, symfony was one of the very first web frameworks to have
automatic output escaping for variables used in templates (2006); and in the
recent years, major frameworks followed the same path:
[Django](http://docs.djangoproject.com/en/dev/releases/1.0/#automatic-escaping-of-template-variables)
has automatic output escaping enabled since 1.0, and Ruby on Rails will also
have it in the upcoming version 3.

Having output escaping enabled by default also means that auditing an
application is much easier. Just have a look at templates where escaping has
been disabled:


```html
{% autoescape off %}
  {{ object.as_html }}
{% endautoescape %}

{{ object.as_html|safe }}

```

{{< notice note >}}
Of course, I'm well aware of the automatic output escaping problems.
You still need to take care of escaping variables in JavaScript tags
correctly, but that's much easier to remember than just having to
escape everything by hand.
{{< /notice >}}

### Sandbox mode

This one is a must-have when you allow users to edit templates (when
webmasters are allowed to edit some templates from a web backend for
instance). That's not a universal need, but one that needs to be addressed
anyway. Evaluating a template in a sandbox means being able to restrict what
can be done in it. For instance, you should be able to restrict the
methods/functions that can be called, the tags that can be used, ...

Neither PHP nor Django have a sandbox mode, so keep reading this article to
learn more about this topic.

Alternative template languages in PHP
-------------------------------------

So, I started looking for a template engine that has all the features I
mentioned above. I found many different template engines but none satisfied
all my needs. The next sections talk about some of them and some others people
suggested in reply to my tweet.

{{< notice note >}}
As PHP have a million template engines, I have only tested and benchmarked
the more "popular" ones. And as I don't use these libraries, the following
sections can contain wrong information. In such a case, please correct me
in the comments and I will then fix this post accordingly.
{{< /notice >}}

### Smarty and Smarty 3

That's the first one that comes to mind. And Django template language itself
was inspired by [Smarty](http://www.smarty.net/). Smarty is the de-facto PHP
standard template engine.


```php
Hello {$name|escape}

{section name=item loop=$items}
  {$items[item]|escape}
{/section}

```

But Smarty suffers from several problems:

 * Not object oriented
 * No template inheritance
 * No sandbox mode
 * No automatic escaping

As far I understand, Smarty 3 is just around the corner and will improve the
library quite a lot:

 * Object oriented architecture
 * Auto escaping of variables
 * Template inheritance

I tested the two versions, but the performance of both are quite bad (see at
the end of this post for more information on the benchmark I did).

### PHPTAL

[PHPTAL](http://phptal.org/) is a very nice project that implements the Zope
Page Templates syntax. It is very well designed, supports lots of features,
but is unable to render templates besides HTML ones, which can be a problem if
you want to use the same language for emails, RSS feeds, and so on.


```xml
&lt;?xml version="1.0"?&gt;
&lt;html&gt;
  &lt;body&gt;
    Hello &lt;span tal:content="name" /&gt;
    &lt;ul tal:repeat="item items"&gt;
      &lt;li tal:content="item"&gt;&lt;/li&gt;
    &lt;/ul&gt;
  &lt;/body&gt;
&lt;/html&gt;

```

Also, I think the syntax is not web designer friendly, especially when you use
advanced features like template inheritance:


```xml
&lt;html metal:use-macro="layout.xml/main"&gt;
 &lt;body metal:fill-slot="content"&gt;
   Hello &lt;span tal:content="name" /&gt;
   &lt;ul tal:repeat="a array"&gt;
     &lt;li tal:content="a"&gt;&lt;/li&gt;
   &lt;/ul&gt;
 &lt;/body&gt;
&lt;/html&gt;

```

-


```xml
&lt;html metal:define-macro="main"&gt;
  &lt;metal:block define-slot="content"/&gt;
&lt;/html&gt;

```

One of the biggest selling point of PHPTAL is the autocompletion you can have
in IDEs, and a guarantee that your HTML is well-formed.

### eZ Components Templates

eZ Components [Template Component](http://www.ezcomponents.org/docs/tutorials/Template)
is also a very nice implementation of a template language. It's probably the
one which have the most features... perhaps too many of them if you ask me:


```php
{use $name}
{use $items}

Hello {$name}

{foreach $items as $item}
 * {$item}
{/foreach}

```

It does not support template inheritance, and my main concern is the
performance. This it is the slowest library I have tested, and by great
margin.

### Dwoo

[Dwoo](http://dwoo.org/) is an interesting project. It is positioned as an
alternative to Smarty. And they did a great job:


```php
&lt;html&gt;
  &lt;body&gt;
    {block "content"}{/block}
  &lt;/body&gt;
&lt;/html&gt;

```

-


```php
{extends "layout.tpl"}

{block "content"}
  {include("basic.tpl")}
{/block}

```

Dwoo mimics Smarty but with some interesting new features like template
inheritance, and with much better performance than Smarty.

Unfortunately, Dwoo has no sandbox feature and its core is not flexible
enough.

### Calypso

[Calypso](http://www.beberlei.de/calypso/) is an implementation of the Django
Template Language in PHP. I mention it because it's a clone of Django and
because some people mentioned it on Twitter. But, the author himself
acknowledge that the implementation is
[flawed](http://www.whitewashing.de/blog/articles/86).

Twig
----

When I started looking for a PHP template language, I focused on libraries
trying to mimic the Django template language features. After many hours of
Googling, I found Twig. Twig was written by [Armin Ronacher](http://lucumr.pocoo.org/)
of [Jinja](http://jinja.pocoo.org/2/documentation/) fame. Needless to say I
have the uttermost respect for Armin as he does a wonderful job with Jinja.
As a matter of fact, Twig is more similar to Jinja than Django as far as the
[implementation](http://lucumr.pocoo.org/2008/9/16/why-jinja-is-not-django-and-why-django-should-have-a-look-at-it)
is concerned.

He wrote [Twig](http://github.com/mitsuhiko/twig) back in 2008 for
[Chypr](http://chyrp.net/), a blogging platform. But he never really developed
it further as he mostly work with Python.

When I had a look at the code, I was immediately sure it was what I was
looking for. The main difference with Calypso being the fact that Twig
compiles the templates down to plain PHP code. I started to use it a bit and
at the end of last week, I asked Armin if he wouldn't mind letting the project
starts a new life. His answer was enthusiastic, and so I began hacking the
code. My version is much different from that of Armin, but the lexer and
parser are mostly the original ones.

I have just hacked the code for a few days, but I'm already quite proud of it,
and I think it's time to open it to the public. So, yesterday I wrote some
[documentation](http://www.twig-project.org/documentation) for it and put up a
simple [website](http://www.twig-project.org/). There is still a lot of work
to do like finishing the documentation, adding more unit tests, and PHPdoc,
but the code is already quite solid and feature-full with:

 * Native template inheritance (templates are compiled as classes);

 * Solid automatic auto-escaping (with no associated runtime overhead as
   everything is done during compilation);

 * Very secure sandbox mode (white-list the tags, filters, and methods that
   can be used in templates);

 * Great extensibility: you override everything, even the core features, by
   bundling your own tags and filters as an extension; but you can also
   manipulate the AST (Abstract Syntax Tree) before compilation. By leveraging
   this possibilities, you can even create your own DSL (Domain Specific Language),
   targeted at your application.

Despite being one the most full-featured PHP templating engines, Twig is also
the fastest one:

 | Library      | Time (sec) | Memory (Ko) | Templates rendered per second
 | ------------ | ---------- | ----------- | -----------------------------
 | Twig         | 3          | 1,190       | 3,333
 | PHPTAL       | 3.8        | 2,100       | 2,632
 | Dwoo         | 6.9        | 1,870       | 1,449
 | Smarty 2     | 12.9       | 2,350       | 775
 | Smarty 3     | 14.9       | 3,230       | 671
 | Calypso      | 34.3       | 620         | 292
 | eZ Templates | 53         | 5,850       | 189

{{< notice note >}}
I benchmarked a template that includes a simple template (one output and a for loop on three items) and decorated with a simple layout.
The times are for the average of ten
runs; a run consisting of one compilation
of the template and 10,000 rendering of it. For engines that do not support
inheritance, I have used a header and a footer instead, and for engines that
do not support automatic output escaping, escaping has been done by hand.
{{< /notice >}}

When the templates are already compiled, the memory consumption is of course
much better for all template engines, and Twig is the one which uses the less
memory:

| Library      | Memory without compilation (Ko)
| ------------ | -------------------------------
| Twig         | 383
| PHPTAL       | 598
| Dwoo         | 1,645
| Smarty 2     | 1,634
| Smarty 3     | 1,790
| Calypso      | 614
| eZ Templates | 2,783

If you want to learn more, please visit the Twig
[website](http://www.twig-project.org/), and start discussing Twig on its
dedicated [mailing-list](http://groups.google.com/group/twig-users). If you
want to join the team, subscribe to the
[developer](http://groups.google.com/group/twig-devs) mailing-list

I have also planned to talk about Twig at the
[Zend UnConference](http://joind.in/talk/view/959). Vote for this talk
and see you there for an interesting discussion about templating engines.



