---
date: '2011-07-16'
title: 'The PHP Ternary Operator: Fast or not?'
summary: |
    People like micro-optimizations. They are easy to understand, easy to apply... and useless. But some time ago, while reviewing pull requests for Twig, I read an interesting discussion about the performance of the ternary operator in PHP. Here is what I learned.
params:
    author: Fabien Potencier
url: /the-php-ternary-operator-fast-or-not.html
aliases:
    - /the-php-ternary-operator-fast-or-not
    - /article/48/the-php-ternary-operator-fast-or-not
---

People like micro-optimizations. They are easy to understand, easy to apply...
and useless. But some time ago, while reviewing pull requests for
[Twig](http://twig-project.org/), I read an interesting discussion about the
performance of the [ternary
operator](https://github.com/fabpot/Twig/issues/380) in PHP (thanks to
[@nikic](https://github.com/nikic) for the investigation).

Do you know which following snippet is the fastest (of course, they do exactly
the same)?


```php
// snippet 1
$tmp = isset($context['test']) ? $context['test'] : '';

// snippet 2
if (isset($context['test'])) {
    $tmp = $context['test'];
} else {
    $tmp = '';
}

```

The right answer is: *it depends*. Most of the time, they are about the same
speed and you don't need to care. But if `$context['test']` contains a large
amount of data, snippet 2 is *much faster* than snippet 1.

Here is the code I have used to test different scenarii:


```php
$context = array('test' => true);

// optionally fill-in the test value with lots of data
for ($i = 0; $i < 100000; $i++) {
    $context['test'][$i] = $i;
}
// you can also just create a big string
// $context = str_repeat(' ', 1000000);

// benchmark
$time = microtime(true);
for ($i = 0; $i < 100; $i++) {
    // the snippet of code to benchmark
    $tmp = isset($context['test']) ? $context['test'] : '';
}
printf("TIME: %0.2d\n", (microtime(true) - $time) * 1000);

```

Note that the absolute performance numbers are meaningless here. We just want
to compare the speed between different snippets.

On my laptop, snippet 1 takes more than two seconds, whereas snippet 2 takes
about 0.05ms. That's a big difference! But if the variable to test does not
host many data, the speed is almost the same.

So, why does the ternary operator become so slow under some circumstances? Why
does it depend on the value stored in the tested variable?

The answer is really simple: the ternary operator always *copies the value*
whereas the `if` statement does not. Why? Because PHP uses a technique known
as *copy-on-write*: When assigning a value to a variable, PHP does not
actually create a copy of the content of the variable until it is modified.

When you write a statement like `$tmp = $context['test']`, very little
happens: the `$tmp` variable just becomes a reference to the
`$context['test']` one; that's why it's really fast. But as soon you want to
modify the variable, PHP needs to copy the original one:


```php
$tmp = $context['test'];

// the copy happens now
$tmp[] = true;

// copy also happens if the original variable changes
// $context['test'][] = true;

```

To sum up, the speed of the ternary operator is directly related to the time
it takes to copy the result of the statement, even if it is not strictly
needed. And copying an array of 100000 elements takes time.

If you are using PHP 5.3, there is a simpler way to express our statement with
the new `?:` construct:


```php
$tmp = $context['test'] ?: '';

```

Unfortunately, this new construct has the same drawbacks as the standard one as far as performance is concerned,
even if PHP should probably be able to optimize the case where the variable
exists.



