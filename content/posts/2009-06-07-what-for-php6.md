---
date: '2009-06-07'
title: What for PHP6?
summary: |
    PHP 5.3 is just around the corner with a lot of great new features. But what for PHP 6?
params:
    author: Fabien Potencier
url: /what-for-php6.html
aliases:
  - /what-for-php6
  - /article/18/what-for-php6
---

PHP 5.3 is just around the corner with a lot of great new features. However,
even if I'm really excited about this new release, I won't make yet another
PHP 5.3 feature list; I will rather look at the future of PHP. PHP core
developers met at php|tek and discussed the future of PHP. And it is really
great to see that they plan lots of wonderful features; let's set aside the
Unicode stuff.

They published some [notes](http://wiki.php.net/summits/pdmnotesmay09) from
the meeting, and here is my personal list for things I find really
interesting:

 * Add `__cast()` magic method that will be called for all casts. If the
   `__toString()` method is there it will get used for string types first.

 * Consider making a "callable" type.

 * Make `ArrayObject` and `ArrayAccess` accepted everywhere regular arrays
   are.

 * Add traits support.

 * Add type hinted return values, scalar type hints.

 * Make function call chaining possible (`f()()` if `f()` returns a function),
   and array dereferencing (`f()[0]`).

 * C#-style properties with getters/setters:


    ```php
    class Foo
    {
       public $bar
           getter { return $this->bar; }
           setter { $this->bar = strtolower($value); }
       ;
    }

    ```



