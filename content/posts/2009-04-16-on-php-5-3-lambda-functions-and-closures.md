---
date: '2009-04-16'
title: On PHP 5.3, Lambda Functions, and Closures
summary: |
    PHP 5.3 will have a lot of exiting new features, and one of the most important one
    for me is the introduction of lambda functions and closures support.
params:
    author: Fabien Potencier
url: /on-php-5-3-lambda-functions-and-closures.html
aliases:
  - /on-php-5-3-lambda-functions-and-closures
  - /article/17/on-php-5-3-lambda-functions-and-closures
---

PHP 5.3 will have a lot of exciting new features. One of the most important one
for me is the introduction of lambda functions and closures support. I won't
talk too much about what lambda functions or closures are, as you can find
many good blog posts describing them in great details. To sum up, a lambda
function is an anonymous PHP function that can be stored in a variable and
passed as an argument to other functions or methods. A closure is a lambda
function that is aware of its surrounding context.

The first obvious use for lambda functions and closures is in conjunction with
the `array_map()`, `array_reduce()`, and `array_filter()` native PHP
functions:


```php
$input = array(1, 2, 3, 4, 5);
$output = array_filter($input, function ($v) { return $v > 2; });

```

The above example filters the input array by removing all values greater than
2:


```php
$output == array(2 => 3, 3 => 4, 4 => 5)

```

`function ($v) { return $v > 2; }` is the lambda function definition and it
can be stored in a PHP variable to be reusable:


```php
$max_comparator = function ($v) { return $v > 2; };

$input = array(1, 2, 3, 4, 5);
$output = array_filter($input, $max_comparator);

```

But what if I want to change the maximum number allowed in the filtered array?
I can either create another lambda function or use a closure:


```php
$max_comparator = function ($max)
{
  return function ($v) use ($max) { return $v > $max; };
};

$input = array(1, 2, 3, 4, 5);
$output = array_filter($input, $max_comparator(2));

```

Now, the `$max_comparator` function takes the maximum allowed number and
returns a function that is different according to this maximum. Even for such
a contrived example, I hope you see the great power it gives you!

Closures also opens up a lot of great possibilities, like the implementation
of the cryptic
[Y-combinator](http://php100.wordpress.com/2009/04/13/php-y-combinator/), as
demonstrated by Stanislav Malyshev in one of his recent blog post:


```php
function Y($F)
{
  $func = function ($f) { return $f($f); };

  return $func(function ($f) use($F)
  {
    return $F(function ($x) use($f)
    {
      $ff = $f($f);

      return $ff($x);
    });
  });
}

```

Today, I want to talk about yet great another example on how to use lambda
functions and closures. You will see that it can simplify your code a lot when
used appropriately.

If you have read my blog recently, you know that I am quite obsessed with
dependency injection these days. This post will show you how to implement a
very simple but still full-featured dependency injection container, thanks to
the new features of PHP 5.3.

A dependency injection container must be able to manage two different kind of
data: objects and parameters. And objects must be created on-demand the first
time they are accessed from the container.

Using a simple class that implements the magic `__get()` and `__set()`
methods, we can easily manage both the objects and the parameters:
 

```php
class DIContainer
{
  protected $values = array();

  function __set($id, $value)
  {
    $this->values[$id] = $value;
  }

  function __get($id)
  {
    return is_callable($this->values[$id]) ? $this->values[$id]($this) : $this->values[$id];
  }
}

```

Using the container is quite simple:


```php
$container = new DIContainer();

// define the parameters
$container->cookie_name = 'SESSION_ID';
$container->storage_class = 'SessionStorage';

// defined the objects
$container->storage = function ($c)
{
  return new $c->storage_class($c->cookie_name);
};
$container->user = function ($c)
{
  return new User($c->storage);
};

// get the user object
$user = $container->user;

// the above call is roughly equivalent to the following code:
// $storage = new SessionStorage('SESSION_ID');
// $user = new User($storage);

```

{{< notice note >}}
The examples I use in this article are the same as the one I have used in my
series on [dependency injection](https://fabien.potencier.org/article/11/what-is-dependency-injection).
{{< /notice >}}

The definition of an object is done by defining a lambda function that returns
an instance of the object.

The `__get()` method checks if the value associated with a key is a PHP
callable (and lambda functions are callables) to make the difference between
an object definition and a parameter.

Also notice how we call the lambda:


```php
$this->values[$id]($this)

```

The trick here is to pass the container as an argument of the lambda function
so that it can use the container to access parameters and other objects
managed by the container.

We can make the container a bit better by adding error support:


```php
class DIContainer
{
  protected $values = array();

  function __set($id, $value)
  {
    $this->values[$id] = $value;
  }

  function __get($id)
  {
    if (!isset($this->values[$id]))
    {
      throw new InvalidArgumentException(sprintf('Value "%s" is not defined.', $id));
    }

    return is_callable($this->values[$id]) ? $this->values[$id]($this) : $this->values[$id];
  }
}

```

Defining objects with lambda functions is great because the developer can do
whatever he wants to actually create and configure instances. But we still
need to implement one important feature of any dependency injection container:
shared instances. This can be done manually like this:


```php
$container->user = function ($c)
{
  static $object;

  if (is_null($object))
  {
    $object = new User($c->storage);
  }

  return $object;
};

```

By declaring a static variable in the lambda function, the first time it is
called, the object is created and returned. For all subsequent calls, the same
instance will always be returned.

It works as expected, but this is quite repetitive, tedious, and error prone.
For all instances that must be unique, we need to add this boilerplate code.
Instead, I want to support shared instances as a native feature of the
container itself. Thanks to closures, that's quite easy to accomplish:


```php
class DIContainer
{
  // ...

  function asShared($callable)
  {
    return function ($c) use ($callable)
    {
      static $object;

      if (is_null($object))
      {
        $object = $callable($c);
      }

      return $object;
    };
  }
}

```

The `asShared()` method wraps the given lambda function to add the needed
logic we have seen before. Declaring an object as unique for a given container
is now dead simple:


```php
$c->user = $c->asShared(function ($c)
{
  return new User($c->storage);
});

```

In less than 30 lines of PHP, we have coded a full-featured dependency
injection container. Quite impressive!

If we remove the need for shared instance, and if we define parameters and
objects as lambdas, we can even compact the code so that it fits in a tweet:


```php
class Container {
 protected $s=array();
 function __set($k, $c) { $this->s[$k]=$c; }
 function __get($k) { return $this->s[$k]($this); }
}

```

I have called this tweet container *twittee*, and it has its own
[dedicated website](http://twittee.org/) and
[github repository](http://github.com/fabpot/twittee/tree/master).

PHP 5.3 is really a great step forward for the PHP language.



