---
date: '2009-12-21'
title: The state of YAML in PHP
summary: |
    My first exposure to YAML was in 2001, back in the days when I was mainly working with Perl.  When I started to use PHP at the end of 2004, one of the first thing that bothered me immediately was the poor support for YAML.
params:
    author: Fabien Potencier
url: /the-state-of-yaml-in-php.html
aliases:
  - /the-state-of-yaml-in-php.html
  - /article/40/the-state-of-yaml-in-php
---

<div style="float: right; padding: 10px">
  <img src="http://components.symfony-project.org/images/components/yaml/home.png" />
</div>

My first exposure to YAML was in 2001, back in the days when I was mainly
working with Perl. Well, I was not using YAML per se at that time, but rather
[`Data::Denter`](http://search.cpan.org/~ingy/Data-Denter-0.15/Denter.pod), a
Perl library that provides data serialization/deserialization. I used this
library mainly for debugging purposes. From its documentation:

  "It formats nested data structures in an indented fashion. It is optimized
  for human readability/editability, safe deserialization, and (eventually)
  speed."

At the end of the year 2002, the module was deprecated in favor of a new
serialization language, [YAML](http://yaml.org/), with the added bonus of
being programming language independent. I promptly switched to use the Perl
[YAML](http://search.cpan.org/~adamk/YAML-0.70/lib/YAML.pm) module, and I
never looked back. I used YAML as a mean to debug my Perl programs, but I also
started to use it more and more to store configuration data.

When I started to use PHP at the end of 2004, one of the first thing that
quickly bothered me was the poor support for YAML in the PHP world.

{{< notice note >}}
By the way, if symfony uses YAML a lot, it has nothing to do with Ruby on
Rails ;) It just happens that Ruby also has some Perl heritage!
{{< /notice >}}

But first, what is YAML?
------------------------

According to the official YAML website, YAML (YAML Ain't Markup Language), is
a **human friendly** data serialization standard for **all programming
languages**.

YAML can be used to describe both simple and complex data structures. It's an
easy to learn language that describes data. As PHP, it has a syntax for simple
types like strings, booleans, floats, integers, arrays, and even more complex
ones like objects.

Nowadays, YAML is a heavily used format for **configuration files**, mainly
because even non programmers are able to understand and modify YAML files
easily.

To sum up the benefits of YAML, I often say that **YAML files are as
expressive as XML files and as readable as INI files**.

{{< notice note >}}
Since the creation of YAML, another lightweight data-interchange format has
come to life: [JSON](http://json.org/). JSON is quite similar to YAML (and as
a matter of fact, JSON is a subset of YAML); but even if it is easy for humans
to read and write, I think it is not as readable as YAML, and a bit too
verbose.
{{< /notice >}}

YAML
----

{{< notice note >}}
If you already know what is YAML and how to use it to describe your data
structures, just skip this section.
{{< /notice >}}

Besides strings, Booleans, and numbers, let's have a look at one of the
simplest configuration structure you can describe with YAML:


```yml
key: value
foo: bar

```

The above snippet is the simplest way to express key/value pairs in YAML. The
`foo` key has a `bar` value. The equivalent PHP code would be:


```php
array('key' => 'value', 'foo' => 'bar')

```

And that's pretty much covers what you can do with ini files. Speaking of ini
files, you can also group key/values under "sections". Here is how this is
possible with YAML:


```yml
section1:
  foo: bar

section2:
  bar: foo

```

The equivalent PHP code reads as follows:


```php
array(
  'section1' => array('foo' => 'bar'),
  'section2' => array('bar' => 'foo'),
)

```

That does the trick because there is several ways to describe key/value pairs.
The short notation (`foo: bar`), and the expanded one, where you use
indentation to describe nested structures as above.

The same data structure can also be described as follows:


```yml
section1: { foo: bar }
section2: { bar: foo }

```

The `{}` is how you enclose a hash. That's one of the greatest benefit of YAML
as a description format: you can **visually organize** your data by using one
of the three possible notations.

Unlike PHP, YAML makes a difference between hashes (mappings) and arrays
(sequences):


```yml
[1, 'a string', "another string"]

```

The above snippet, a YAML sequence, is the equivalent of the following PHP
code:


```php
array(1, 'a string', "another string")

```

If you mix and match mappings and sequences, short and verbose notations, you
can describe very complex data structures:


```yml
section1:
  foo: { bar: foo }
  bar: [1, 2]
  foobar:
    - 'a string'
    - 'another one'

```

{{< notice note >}}
This section has barely scratched the surface of what you can express with
YAML. If you want to learn more, you will find plenty of
[documentation](http://components.symfony-project.org/yaml/trunk/book/02-YAML)
on the Internet.
{{< /notice >}}

YAML in PHP
-----------

YAML is human-friendly, but not so developer-friendly for someone willing to
write a parser for it. The YAML specification is really huge. If you
[read](http://www.yaml.org/spec/1.2/spec.html) it carefully, you can easily
imagine that writing a YAML parser is not an easy task. As I mainly use YAML
as a configuration format like many other developers, I'm more looking for a
fast, incomplete but correct library, instead of a fat, spec-compliant one.

Back in 2005, I was looking for such a YAML parser and dumper for PHP. Chris
[Wanstrath](http://ozmm.org/), who will eventually create
[Github](http://github.com/) some years later, wrote one such limited parser
and dumper, [Spyc](http://code.google.com/p/spyc/), specifically to be used as
a simple configuration library.

I used it for [symfony](http://symfony-project.org/) 1.0. I fixed some bugs
from time to time, but as time passed, I found many limitations and became
more and more frustrated about it. One day, I eventually decided to write a
more robust and stable YAML parser and dumper for symfony.

{{< notice note >}}
Since then, Alexey Zakhlestin created a
[PECL](http://pecl.php.net/package/syck) extension that wraps the Syck
library.
{{< /notice >}}

At the beginning of 2009, I decided to release this library as a standalone
library, with no dependency whatsoever. It means that you can start using it
[today](http://components.symfony-project.org/yaml/).

The YAML Symfony Component
--------------------------

Released under the MIT license, the YAML Symfony Component can be used in any
application, even commercial ones.

When I created this YAML library for PHP, I had several goals in mind:

  * *Ease of use*: Installation should be easy and fast. Install it via PEAR,
    download an archive, or checkout the SVN or Git repository, and you are
    ready to go. No configuration. Drop the files in a directory and start
    using it right away.

  * *Fast*: One of the main goal of Symfony YAML was to find the right balance
    between speed and features.

  * *Unit tested*: The library is unit-tested (with more than 400 unit tests
    as of today).

  * *"Real" Parser*: To correctly handle a large subset of the YAML
    specification, a dedicated and hand-written parser has been written. The
    parser is robust, easy to understand, and simple enough to extend.

  * *Clear error messages*: Whenever you have a syntax problem with your YAML
    files, the library should output helpful messages with the filename and
    the line number where the problem occurred. It eases debugging a lot.

And of course, YAML being not so well-known in the PHP world, the YAML
component also comes with a full
[documentation](http://components.symfony-project.org/yaml/documentation).

The easiest way to
[install](http://components.symfony-project.org/yaml/installation) the Symfony
YAML Component is probably to use the PEAR installer:

    $ pear channel-discover pear.symfony-project.com
    $ pear install symfony/YAML

Using YAML in your Projects
---------------------------

The Symfony YAML library consists of two main classes: one to parse YAML
strings, and the other to dump a PHP variable to a YAML string. On top of
these two core classes, the main `sfYaml` class acts as a thin wrapper and
simplifies common uses:


```php
// loading a YAML file or a YAML string
$var = sfYaml::load('/path/to/file.yml');

// Dumping a PHP variable to YAML
$yaml = sfYaml::dump($var, $inline);

```

YAML for PHP 5.3
----------------

The previous sections use the PHP 5.2 compatible version of the library. If
you have already switched to use PHP 5.3, the good news is that the YAML
Component is already available for that version too. For now, it is only
available on the Symfony 2 Subversion repository:

    $ svn co http://svn.symfony-project.com/branches/2.0/lib/Symfony/Components/YAML/ YAML

-


```php
use Symfony\Components\YAML\YAML;

// loading a YAML file or a YAML string
$var = YAML::load('/path/to/file.yml');

// Dumping a PHP variable to YAML
$yaml = YAML::dump($var, $inline);

```

This version can be autoloaded with any autoloader that follows the
[standards](http://groups.google.com/group/php-standards/web/psr-0-final-proposal)
discussed by some PHP developers. Symfony 2 provides such an autoloader:


```php
require_once __DIR__.'/lib/Symfony/Foundation/ClassLoader.php';

use Symfony\Foundation\ClassLoader;

$loader = new ClassLoader('Symfony', __DIR__.'/lib');
$loader->register();

```

The YAML Symfony Component is already used by and bundled with many popular
Open-Source PHP software like symfony, Doctrine, and PHPUnit. Other frameworks
like the upcoming [Okapi2](http://okapi.liip.ch/) framework and the
[mootools](http://mootools.net/forge/) plugins repository,
[announced](http://mootools.net/blog/2009/12/10/the-official-mootools-plugins-repository-is-here/)
some days ago, make a heavy use of YAML and also use the YAML Symfony
Component.

Next time you look for a flexible mean to store or share data, consider using
YAML!



