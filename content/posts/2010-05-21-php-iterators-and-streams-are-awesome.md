---
date: '2010-05-21'
title: PHP Iterators and Streams are awesome
summary: |
    You should learn how to take advantages of PHP Iterators and Streams.
params:
    author: Fabien Potencier
url: /php-iterators-and-streams-are-awesome.html
aliases:
    - /php-iterators-and-streams-are-awesome
    - /article/44/php-iterators-and-streams-are-awesome
---

A lot of people complain about "problems" with PHP as a language (function
name inconsistencies, `register_globals`, and much more).

But PHP also have a lot of awesome features; at least two of them are in my
opinion largely underused: *Iterators* and *Streams*. You won't learn how to
use them in this post, but with a simple example, I hope you will want to
learn more about them.

Some weeks ago, I have rewritten the old `sfFinder` class for Symfony 2 using
Iterators. It was a really great experience and it makes for a very extensible
Finder Component.

The Finder class has been designed to be used to find files and directories
locally on your machine. But there is more. What if I want to use the same
interface for some Amazon S3 bucket? Is it possible? Of course it is, thanks
to streams:


```php
use Symfony\Components\Finder\Finder;

$s3 = new \Zend_Service_Amazon_S3($key, $secret);
$s3->registerStreamWrapper("s3");

$finder = new Finder();
$finder->name('photos*')->size('< 100K')->date('since 1 hour ago');
foreach ($finder->in('s3://bucket-name') as $file) {
    // do something

    print $file->getFilename()."\n";
}

```

Want to learn more? Read the documentation about
[Iterators](http://uk2.php.net/manual/en/spl.iterators.php) and
[Streams](http://www.php.net/streams) on php.net.



