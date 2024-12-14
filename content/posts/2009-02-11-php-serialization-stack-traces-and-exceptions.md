---
date: '2009-02-11'
title: PHP Serialization, Stack Traces, and Exceptions
summary: |
    Yesterday, I fixed a [bug](http://trac.symfony-project.org/ticket/5267) that
    looks very weird at first. In this post, I will describe the problem, the
    solution I found, and explain some PHP behaviors in the process.
params:
    author: Fabien Potencier
url: /php-serialization-stack-traces-and-exceptions.html
aliases:
  - /article/9/php-serialization-stack-traces-and-exceptions
  - /php-serialization-stack-traces-and-exceptions
---

Yesterday, I fixed a [bug](http://trac.symfony-project.org/ticket/5267) that
looks very weird at first. In this post, I will describe the problem, the
solution I found, and explain some PHP behaviors in the process.

The Bug Report
--------------

First the bug report: when trying to serialize a symfony form instance, a PDO
exception was thrown:

    "You cannot serialize or unserialize PDO instances"

This exception is thrown by PDO because PDO instances are not serializable for
good reasons.

But it is weird because the `sfForm` class does not depend on PDO. How is it
possible?

The Problem
-----------

After some investigation, we discovered that the bug was only for people with
sessions stored in the database using PDO. So, we looked if the form instance
was somewhat tied to the session, with no luck. The `sfForm` class has no
dependency except for the widget classes, the validators classes, and the
validation error classes. So, we tried to reproduce the bug with just a
widget, just a validator, and just a validator error class.

Surprisingly enough, the problem came from the validation error classes. In
symfony, the validator error classes extends the PHP `Exception` class.

Serializing an `Exception` instance is enough to reproduce the bug if there is
a PDO instance "flying around" as demonstrated by this code:


```php
$dbh = new PDO('sqlite:memory:');

function will_crash($dbh)
{
  // serialize an exception
  echo serialize(new Exception());
}

// this will throw a PDOException
will_crash($dbh);

```

What happens? When PHP serializes an exception, it serializes the exception
code, the exception message, but also the
[stack trace](http://www.php.net/manual/en/exception.gettrace.php).

The stack trace is an array containing all functions and methods that have
already been executed at this point of the script. The trace contains the file
name, the line in the file, the function name, and an array of all arguments
passed to the function. Do you spot the problem?

The stack trace contains a reference to the PDO instance, as it was passed to
the `will_crash()` function, and as PDO instances are not serializable, an
exception is thrown when PHP serializes the stack trace.

So, *whenever a non-serializable object is present in the stack trace, the
exception won't be serializable*.

The Solution
------------

In PHP, you can override the serialization process by implementing the
[`Serializable`](http://www.php.net/serializable) interface. The solution was
then simple enough:


```php
class sfValidatorError extends Exception implements Serializable
{
  // class code

  public function serialize()
  {
    return serialize(array($this->validator, $this->arguments, $this->code, $this->message));
  }

  public function unserialize($serialized)
  {
    list($this->validator, $this->arguments, $this->code, $this->message) = unserialize($serialized);
  }
}

```

The [`serialize()`](http://www.php.net/manual/en/serializable.serialize.php)
method should return a string that represents the object. In our case, we just
serialize all properties, except the stack trace.

The [`unserialize()`](http://www.php.net/manual/en/serializable.unserialize.php)
method takes the serialized string as an argument and should initialize the
object as it replaces the call to the `__construct()` method.

The Tests
---------

So far so good. To ensure that the problem was fixed, I needed a way to write
some tests. But I didn't want to rely on PDO for tests. Simulating the
behavior of PDO is simple enough. We can just create a class that cannot be
serialized:


```php
class NotSerializable implements Serializable
{
  public function serialize()
  {
    throw new LogicException('You cannot serialize or unserialize NotSerializable instances');
  }

  public function unserialize($serialized)
  {
    throw new LogicException('You cannot serialize or unserialize NotSerializable instances');
  }
}

```



