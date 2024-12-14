---
date: '2010-04-22'
title: Find your Files
summary: |
    How do you find files and directories with PHP? Do you use Iterators? If not, you probably should... or keep reading for a better and simpler way.
params:
    author: Fabien Potencier
url: /find-your-files.html
aliases:
  - /find-your-files
  - /article/43/find-your-files
---

The best practices for finding files with PHP has evolved a lot in a the last
few years. Back in 2004, one of the very first thing I did with PHP was
porting the `File::Find::Rule` Perl module to PHP. `File::Find::Rule` is a
great way to describe the files and directories you want to work with. I used
the `opendir`, `readdir`, and `closedir` native PHP functions, and it did the
job quite well. The PHP class was named `sfFinder`, and it can still be found
in all symfony versions. Even if the class is bundled with symfony, I know
that a few people use it for all kind of stuff, not necessarily related to
symfony.

But the code starts to show its age; first because I learned a lot since then
about PHP, and also because there is a better way now. Enter
[iterators](http://www.php.net/manual/en/spl.iterators.php)! PHP 5 comes
bundled with a bunch of iterator classes that ease all kind of, well,
iterations. You can iterate over an iterator with the standard `foreach`
operator, a very powerful PHP construct.

PHP Iterators
-------------

So, how do you get all the files and directories recursively with PHP
iterators? Frankly, I don't know. Well, I know more or less which classes to
use and how to assemble them, but instead of thinking too hard, I always copy
and paste an existing snippet of code to get it right. Here is such a snippet:


```php
// some flags to filter . and .. and follow symlinks
$flags = \FilesystemIterator::SKIP_DOTS | \FilesystemIterator::FOLLOW_SYMLINKS;

// create a simple recursive directory iterator
$iterator = new \RecursiveDirectoryIterator($dir, $flags);

// make it a truly recursive iterator
$iterator = new \RecursiveIteratorIterator($iterator, \RecursiveIteratorIterator::SELF_FIRST);

// iterate over it
foreach ($iterator as $file)
{
  // do something with $file (a \SplFileInfo instance)
}

```

{{< notice note >}}
Noticed the fancy `\` character before each built-in class? That's the way you
reference built-in PHP class when using them in a PHP 5.3 namespace context.
{{< /notice >}}

As you can see for yourself, nothing complex. You just need to know the which
Iterator to use, their possible flags, and how to compose them together. So,
the first barrier of entry is the learning curve. There are a lot of great
tutorials and presentations on the Internet about iterators, but the official
documentation on php.net probably lacks some good examples.

The other "problem" is that everything is very object-oriented. And as soon as
you want to filter the iterator, you will need to create your own classes,
which seems impractical most of the time. That's because PHP iterators are
very powerful and have been written to be general-purpose iterators.

What is filtering? Let's say I want to exclude all files ending with `.rb`
from the iterator. I can create a simple `\FilterIterator` for that:


```php
class ExcludeRubyFilesFilterIterator extends \FilterIterator
{
  public function accept()
  {
    $fileinfo = $this->getInnerIterator()->current();

    if (preg_match('/\.rb$/', $fileinfo))
    {
      return false;
    }

    return true;
  }
}

```

This filter iterator can be used with the previous one by wrapping it like
this:


```php
$iterator = new ExcludeRubyFilesFilterIterator($iterator);

```

That's easy enough. But when I need to find files and directories, I always
need the same kind of specialized filters, like excluding VCS files (like
`.svn` and `.git` directories), filtering files by name or by size.

The Symfony Finder Component
----------------------------

Instead of writing the same iterators over and over again, I have packaged
them in a Symfony Component: the Finder component.

The Symfony Finder Component provides many specialized Iterator classes for
finding files and directories. It also adds a wrapper on top of them to ease
its day-to-day usage.

As any Symfony component, you first need to bootstrap your script with any
class loader that is able to load classes that follows the PHP 5.3
interoperability standards, like the Symfony `UniversalClassLoader` class:


```php
require_once '/path/to/src/Symfony/Foundation/UniversalClassLoader.php';

use Symfony\Foundation\UniversalClassLoader;

$classLoader = new UniversalClassLoader();
$classLoader->registerNamespace('Symfony', '/path/to/src');
$classLoader->register();

```

Now, let's see how to use the Finder class, the main class of the component:

    use Symfony\Components\Finder\Finder;

    $finder = new Finder();
    $iterator = $finder->files()->in(__DIR__);

    foreach ($iterator as $file)
    {
      print $file->getRealpath()."\n";
    }

The above code prints the names of all the files in the current directory
recursively. Notice that the Finder class uses a fluent interface, which means
that all methods return the Finder instance. The only exception is the `in()`
method, which builds and returns an Iterator for the given directory, or for
an array of directories:


```php
$iterator = $finder->files()->in(array('/path1', '/path2'));

```

{{< notice tip >}}
You can convert an iterator to an array with the `iterator_to_array()` method,
and have the number of items with `iterator_count()`.
{{< /notice >}}

If you want to restrict the iterator to only return PHP files in the current
directory, use the `name()` and `maxDepth()` methods:


```php
$iterator = $finder
  ->files()
  ->name('*.php')
  ->maxDepth(0)
  ->in(__DIR__);

```

The `name()` method accepts globs, strings, or regexes:


```php
$finder
  ->files()
  ->name('/\.php$/');

```

There is also methods to exclude files by name or to exclude whole directories
content from matching:


```php
$finder
  ->files()
  ->name('test.*')
  ->notName('*.rb')
  ->exclude('ruby');

```

The result should contain files named `test` with any extension, but not the
ones ending with `.rb` (it excludes `test.rb`), and the iterator won't match
any file in `ruby` directories (`ruby/foo/test.php` won't match for instance).

If you want to follow links, use the `followLinks()` method:


```php
$finder
  ->files()
  ->followLinks();

```

You can also restrict files by size:


```php
$finder
  ->files()
  ->name('/\.php$/')
  ->size('< 1.5K');

```

Most of the methods are cumulative. So, if you want to get all PHP and Python
files with a size between 1 and 2 K, here is the code:


```php
$finder
  ->files()
  ->name('*.php')
  ->name('*.py/')
  ->size('>= 1K')
  ->size('<= 2K');

```

{{< notice note >}}
By default, the iterator ignores popular VCS files. This can be changed with
the `ignoreVCS()` method.
{{< /notice >}}

As the `in()` method returns an `\Iterator` instance, you can wrap it with
your own specialized iterator. But instead of creating a class, you can also
use the `filter()` method:


```php
$filter = function (\SplFileInfo $fileinfo)
{
  if (strlen($fileinfo) > 10)
  {
    return false;
  }
};

$finder
  ->files()
  ->name('*.php')
  ->filter($filter);

```

This example excludes all the files with a file name of more than 10
characters.

Want to sort the result by name, use the `sortByName()` method:


```php
$finder
  ->files()
  ->name('*.php')
  ->sortByName();

```

{{< notice note >}}
Notice that the `sort*` methods need to get all matching elements to do their
jobs. For large iterators, it can be rather slow.
{{< /notice >}}

Under the hood, the Finder class uses specialized iterator classes:

 * `ChainIterator`
 * `CustomFilterIterator`
 * `DateRangeFilterIterator` (coming soon)
 * `ExcludeDirectoryFilterIterator`
 * `FileTypeFilterIterator`
 * `FilenameFilterIterator`
 * `IgnoreVcsFilterIterator`
 * `LimitDepthFilterIterator`
 * `SizeRangeFilterIterator`
 * `SortableIterator`

Have a look at the
[code](http://github.com/symfony/symfony/tree/master/src/Symfony/Components/Finder/)
to learn more about these iterators and how they work.



