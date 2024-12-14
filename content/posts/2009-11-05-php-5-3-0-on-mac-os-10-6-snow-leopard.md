---
date: '2009-11-05'
title: PHP 5.3.0 on Mac OS 10.6 (Snow Leopard)
summary: |
    How I compiled PHP 5.3 on my Snow Leopard laptop.
params:
    author: Fabien Potencier
url: /php-5-3-0-on-mac-os-10-6-snow-leopard.html
aliases:
    - /php-5-3-0-on-mac-os-10-6-snow-leopard
    - /article/37/php-5-3-0-on-mac-os-10-6-snow-leopard
---

Recently, I started to use PHP 5.3.0 extensively, thanks to the
[move](http://www.symfony-project.org/blog/2009/10/27/why-will-symfony-2-0-finally-use-php-5-3)
of Symfony 2 to PHP 5.3. To do my work, I need to have several different
versions of PHP at hand, with different configurations, and so I always
compile my own PHP and Apache binaries on my Mac. But as Snow Leopard comes
bundled with PHP 5.3.0, I started some PHP 5.3.0 experiments with the bundle
version and everything went well for a couple of days.

Now that I use PHP 5.3.0 more and more, I decided it was time to compile my
own version. As it turns out, compiling PHP 5.3.0 on Snow Leopard is a bit
involving. As I loose some time before finding the right
[solution](http://bugs.php.net/bug.php?id=49267), here is what I did step by
step (I have just followed the tips referenced in the
[ticket](http://bugs.php.net/bug.php?id=49267) opened for the issue on
php.net, so all credits go to them).

First, I ran the configure script with my usual options and added
`--with-iconv-dir=/usr` at the end:

    $ ./configure ... --with-iconv-dir=/usr

{{< notice note >}}
If you have upgraded your Mac to Snow Leopard, you should know that everything
must now be compiled for 64 bits. It means that you might need to recompile extra
libraries needed by PHP like libjpeg or libpng, or even re-install a 64 bits version
of MySQL.
{{< /notice >}}

I then patched the `iconv.c` file the same way
[Apple](http://opensource.apple.com/source/apache_mod_php/apache_mod_php-53/patches/iconv.patch)
did for their own version:


``` bash
--- php-5.3.0/ext/iconv/iconv.c	2009-03-16 22:31:04.000000000 -0700
+++ php/ext/iconv/iconv.c	2009-07-15 14:40:09.000000000 -0700
@@ -51,9 +51,6 @@
 #include <gnu/libc-version.h>
 #endif

-#ifdef HAVE_LIBICONV
-#undef iconv
-#endif

 #include "ext/standard/php_smart_str.h"
 #include "ext/standard/base64.h"
@@ -182,9 +179,6 @@
 }
 /* }}} */

-#ifdef HAVE_LIBICONV
-#define iconv libiconv
-#endif

 /* {{{ typedef enum php_iconv_enc_scheme_t */
 typedef enum _php_iconv_enc_scheme_t {
```


To apply the patch, save the above text in a file (`patch`) and from the root
source of PHP, type the following command:


``` bash
$ patch -p1 < patch
```


Then, add `-lresolv` to the `EXTRA_LIBS` variable:


```bash
$ export EXTRA_LIBS=-lresolv 
```


Eventually, run the usual `make` commands


```bash
$ make
$ make install
```

