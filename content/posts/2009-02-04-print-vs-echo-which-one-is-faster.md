---
date: '2009-02-04'
title: print vs echo, which one is faster?
summary: |
    As probably many of you, I am tired to read blog posts about non-sense
    micro-optimizations like replacing `print` by `echo`, `++$i` by `$i++`, or
    double quotes by single quotes. Why? Because 99.999999% of the time, you don't
    care. Why? Because 99.99% of the time, you'd better install a PHP accelerator
    like APC, or add these missing indexes on some
    database columns, or try to avoid those 1000 database requests per page.
params:
    author: Fabien Potencier
url: /print-vs-echo-which-one-is-faster.html
aliases:
    - /article/8/print-vs-echo-which-one-is-faster
    - /print-vs-echo-which-one-is-faster
---

As most of us, I am tired to read blog posts about non-sense
micro-optimizations like replacing `print` by `echo`, `++$i` by `$i++`, or
double quotes by single quotes. Why? Because 99.999999% of the time, it is
irrelevant. Why? Because 99.99% of the time, you'd better install a PHP
accelerator like [APC](http://www.php.net/APC), or add these missing indexes
on your database columns, or try to avoid those 1000 database requests you
have on the homepage.

But let's pretend you really want to know the answer to this question. Instead
of trying to write a script and execute it a million times to find the
fastest, I want to show you some interesting tools you might find useful from
time to time as they allow you to better understand the PHP code you write.

Enter [VLD](http://derickrethans.nl/vld.php), the "Vulcan Logic Disassembler".
VLD is written by [Derrick Rethans](http://derickrethans.nl/) and as you can
read on the VLD homepage, it "hooks into the Zend Engine and dumps all the
opcodes (execution units) of a script".

Installing VLD is simple enough. Download the
[latest version](http://pecl.php.net/get/vld), and install it as you would have
done for any other PHP extension:

    $ phpize
    $ ./configure
    $ sudo make install

Enable the extension in your `php.ini` file:


```ini
extension=vld.so

```

Time to look under the hood. Create two simple files, one uses `echo`, and
the other one uses `print`:


```php
// print.php
<?php print 'foo';

// echo.php
<?php echo 'foo';

```

Execute these scripts from the command-line with the `-d vld.activate=1`
argument to activate VLD output and let's have a look at the opcodes generated
by these scripts:

    $ php -d vld.active=1 print.php

-

    number of ops:  4
    compiled vars:  none
    line     #  op                           fetch          ext  return  operands
    -------------------------------------------------------------------------------
       1     0  PRINT                                            ~0      'foo'
             1  FREE                                                     ~0
       2     2  RETURN                                                   1
             3* ZEND_HANDLE_EXCEPTION                                    

-

    $ php -d vld.active=1 echo.php

-

    number of ops:  3
    compiled vars:  none
    line     #  op                           fetch          ext  return  operands
    -------------------------------------------------------------------------------
       1     0  ECHO                                                     'foo'
       2     1  RETURN                                                   1
             2* ZEND_HANDLE_EXCEPTION                                    

Spot the difference? Yes, **`print` uses one more opcode** because it actually
returns something. We can conclude that `echo` is faster than `print`. But one
opcode costs nothing, really nothing. Even if a script have
hundreds of calls to `print`.

{{< notice tip >}}
Because `print` always returns `1`, you can do interesting things like the
following, which is impossible with `echo`:

     [php]
     <?php $isFoo and print 'foo'; ?>
{{< /notice >}}

Want to know the number of opcodes executed when running a script that has a
lot of includes? Try this:

    $ php -d vld.active=1 print.php 2> output
    $ grep "number of ops" output | cut -f 5 -d ' ' | (tr '\n' +; echo 0) | bc

I have tried on a fresh Wordpress installation. The script halts before it
ends with a "Bus Error" on my laptop, but the number of opcodes was already at
more than 2.3 millions. Enough said.



