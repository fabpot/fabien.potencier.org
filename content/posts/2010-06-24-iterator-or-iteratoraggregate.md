---
date: '2010-06-24'
title: Iterator or IteratorAggregate?
summary: |
    A quick tip on iterators.
params:
    author: Fabien Potencier
url: /iterator-or-iteratoraggregate.html
aliases:
    - /iterator-or-iteratoraggregate
    - /article/45/iterator-or-iteratoraggregate
---

In my last two posts, I talked about PHP iterators. Here is a quick tip on the
same topic.

If you have ever used iterators in your code, you have probably implemented
the `Iterator` interface. Objects of a class that implements `Iterator` can be
iterated over with the `foreach` loop:


```php
$foo = new Foo();

foreach ($foo as $key => $value) {
    // do something with $key and $value
}

```

The `Iterator` interface has five simple methods that must be implemented:


```php
class Foo implements Iterator
{
    protected $attributes;

    public function rewind()
    {
        reset($this->attributes);
    }

    public function current()
    {
        return current($this->attributes);
    }

    public function key()
    {
        return key($this->attributes);
    }

    public function next()
    {
        return next($this->attributes);
    }

    public function valid()
    {
        return false !== current($this->attributes);
    }
}

```

The `IteratorAggregate` interface is quite similar (both interfaces implement
`Traversable`) but creates an external `Iterator`. But when the iterator is based 
on an array, creating an external `Iterator` for this array gives you a more concise
and more readable code:


```php
class Foo implements IteratorAggregate
{
    protected $attributes;

    public function getIterator()
    {
        return new ArrayIterator($this->attributes);
    }
}

```



