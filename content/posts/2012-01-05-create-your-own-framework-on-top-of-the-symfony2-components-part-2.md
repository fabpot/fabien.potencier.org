---
date: '2012-01-05'
title: Create your own framework... on top of the Symfony2 Components (part 2) 
summary: |
    In this second part of the series, we are going to use the Symfony2 HttpFoundation component.
params:
    author: Fabien Potencier
url: /create-your-own-framework-on-top-of-the-symfony2-components-part-2.html
aliases:
    - /create-your-own-framework-on-top-of-the-symfony2-components-part-2
    - /article/51/create-your-own-framework-on-top-of-the-symfony2-components-part-2
---

{{< notice note >}}
This article is part of a series of articles that explains how to create a
framework with the Symfony Components. It is **OBSOLETE** but [an up-to-date
version can be found in the Symfony
documentation](http://symfony.com/doc/current/create_framework/index.html).
{{< /notice >}}

Before we dive into the code refactoring, I first want to step back and take a
look at why you would like to use a framework instead of keeping your
plain-old PHP applications as is. Why using a framework is actually a good
idea, even for the simplest snippet of code and why creating your framework on
top of the Symfony2 components is better than creating a framework from
scratch.

{{< notice note >}}
I won't talk about the obvious and traditional benefits of using
a framework when working on big applications with more than a few
developers; the Internet has already plenty of good resources on that
topic.
{{< /notice >}}

Even if the "application" we wrote yesterday was simple enough, it suffers
from a few problems:


```php
<?php

// framework/index.php

$input = $_GET['name'];

printf('Hello %s', $input);

```

First, if the `name` query parameter is not given in the URL query string,
you will get a PHP warning; so let's fix it:


```php
<?php

// framework/index.php

$input = isset($_GET['name']) ? $_GET['name'] : 'World';

printf('Hello %s', $input);

```

Then, this *application is not secure*. Can you believe it? Even this simple
snippet of PHP code is vulnerable to one of the most widespread Internet
security issue, XSS (Cross-Site Scripting). Here is a more secure version:


```php
<?php

$input = isset($_GET['name']) ? $_GET['name'] : 'World';

header('Content-Type: text/html; charset=utf-8');

printf('Hello %s', htmlspecialchars($input, ENT_QUOTES, 'UTF-8'));

```

{{< notice note >}}
As you might have noticed, securing your code with `htmlspecialchars` is
tedious and error prone. That's one of the reasons why using a template engine
like [Twig](http://twig.sensiolabs.com/), where auto-escaping is enabled by default, might be a good idea
(and explicit escaping is also less painful with the usage of a simple `e`
filter).
{{< /notice >}}

As you can see for yourself, the simple code we had written first is not that
simple anymore if we want to avoid PHP warnings/notices and make the code
more secure.

Beyond security, this code is not even easily testable. Even if there is not
much to test, it strikes me that writing unit tests for the simplest possible
snippet of PHP code is not natural and feels ugly. Here is a tentative PHPUnit
unit test for the above code:


```php
<?php

// framework/test.php

class IndexTest extends \PHPUnit_Framework_TestCase
{
    public function testHello()
    {
        $_GET['name'] = 'Fabien';

        ob_start();
        include 'index.php';
        $content = ob_get_clean();

        $this->assertEquals('Hello Fabien', $content);
    }
}

```

{{< notice note >}}
If our application were just slightly bigger, we would have been able to find
even more problems. If you are curious about them, read the [Symfony2 versus Flat PHP](http://symfony.com/doc/current/book/from_flat_php_to_symfony2.html) chapter of the Symfony2 documentation.
{{< /notice >}}

At this point, if you are not convinced that security and testing are indeed
two very good reasons to stop writing code the old way and adopt a framework
instead (whatever adopting a framework means in this context), you can stop
reading this series now and go back to whatever code you were working on
before.

{{< notice note >}}
Of course, using a framework should give you more than just security and
testability, but the more important thing to keep in mind is that the
framework you choose must allow you to write better code faster.
{{< /notice >}}

Going OOP with the HttpFoundation Component
-------------------------------------------

Writing web code is about interacting with HTTP. So, the fundamental
principles of our framework should be centered around the [HTTP specification](http://tools.ietf.org/wg/httpbis/).

The HTTP specification describes how a client (a browser for instance)
interacts with a server (our application via a web server). The dialog between
the client and the server is specified by well-defined *messages*, requests
and responses: *the client sends a request to the server and based on this
request, the server returns a response*.

In PHP, the request is represented by global variables (`$_GET`, `$_POST`,
`$_FILE`, `$_COOKIE`, `$_SESSION`...) and the response is generated by
functions (`echo`, `header`, `setcookie`, ...).

The first step towards better code is probably to use an Object-Oriented
approach; that's the main goal of the Symfony2 HttpFoundation component:
replacing the default PHP global variables and functions by an Object-Oriented
layer.

To use this component, open the `composer.json` file and add it as a
dependency for the project:

    # framework/composer.json
    {
        "require": {
            "symfony/class-loader": "2.1.*",
            "symfony/http-foundation": "2.1.*"
        }
    }

Then, run the composer `update` command:

    $ php composer.phar update

Finally, at the bottom of the `autoload.php` file, add the code needed to
autoload the component:


```php
<?php

// framework/autoload.php

$loader->registerNamespace('Symfony\\Component\\HttpFoundation', __DIR__.'/vendor/symfony/http-foundation');

```

Now, let's rewrite our application by using the `Request` and the
`Response` classes:


```php
<php

// framework/index.php

require_once __DIR__.'/autoload.php';

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

$request = Request::createFromGlobals();

$input = $request->get('name', 'World');

$response = new Response(sprintf('Hello %s', htmlspecialchars($input, ENT_QUOTES, 'UTF-8')));

$response->send();

```

The `createFromGlobals()` method creates a `Request` object based on the
current PHP global variables.

The `send()` method sends the `Response` object back to the client (it
first outputs the HTTP headers followed by the content).

{{< notice tip >}}
Before the `send()` call, we should have added a call to the `prepare()`
method (`$response->prepare($request);`) to ensure that our Response were
compliant with the HTTP specification. For instance, if we were to call the
page with the `HEAD` method, it would have removed the content of the
Response.
{{< /notice >}}

The main difference with the previous code is that you have total control of
the HTTP messages. You can create whatever request you want and you are in
charge of sending the response whenever you see fit.

{{< notice note >}}
We haven't explicitly set the `Content-Type` header in the rewritten code as
the charset of the Response object defaults to `UTF-8`.
{{< /notice >}}

With the `Request` class, you have all the request information at your
fingertips thanks to a nice and simple API:


```php
<?php

// the URI being requested (e.g. /about) minus any query parameters
$request->getPathInfo();

// retrieve GET and POST variables respectively
$request->query->get('foo');
$request->request->get('bar', 'default value if bar does not exist');

// retrieve SERVER variables
$request->server->get('HTTP_HOST');

// retrieves an instance of UploadedFile identified by foo
$request->files->get('foo');

// retrieve a COOKIE value
$request->cookies->get('PHPSESSID');

// retrieve an HTTP request header, with normalized, lowercase keys
$request->headers->get('host');
$request->headers->get('content_type');

$request->getMethod();    // GET, POST, PUT, DELETE, HEAD
$request->getLanguages(); // an array of languages the client accepts

```

You can also simulate a request:


```php
$request = Request::create('/index.php?name=Fabien');

```

With the `Response` class, you can easily tweak the response:


```php
<?php

$response = new Response();

$response->setContent('Hello world!');
$response->setStatusCode(200);
$response->headers->set('Content-Type', 'text/html');

// configure the HTTP cache headers
$response->setMaxAge(10);

```

{{< notice tip >}}
To debug a Response, cast it to a string; it will return the HTTP
representation of the response (headers and content).
{{< /notice >}}

Last but not the least, these classes, like every other class in the Symfony
code, have been [audited](http://symfony.com/blog/symfony2-security-audit) for security issues by an independent company. And
being an Open-Source project also means that many other developers around the
world have read the code and have already fixed potential security problems.
When was the last you ordered a professional security audit for your home-made
framework?

Even something as simple as getting the client IP address can be insecure:


```php
<?php

if ($myIp == $_SERVER['REMOTE_ADDR']) {
    // the client is a known one, so give it some more privilege
}

```

It works perfectly fine until you add a reverse proxy in front of the
production servers; at this point, you will have to change your code to make
it work on both your development machine (where you don't have a proxy) and
your servers:


```php
<?php

if ($myIp == $_SERVER['HTTP_X_FORWARDED_FOR'] || $myIp == $_SERVER['REMOTE_ADDR']) {
    // the client is a known one, so give it some more privilege
}

```

Using the `Request::getClientIp()` method would have given you the right
behavior from day one (and it would have covered the case where you have
chained proxies):


```php
<?php

$request = Request::createFromGlobals();

if ($myIp == $request->getClientIp()) {
    // the client is a known one, so give it some more privilege
}

```

And there is an added benefit: it is *secure* by default. What do I mean by
secure? The `$_SERVER['HTTP_X_FORWARDED_FOR']` value cannot be trusted as it
can be manipulated by the end user when there is no proxy. So, if you are
using this code in production without a proxy, it becomes trivially easy to
abuse your system. That's not the case with the `getClientIp()` method as
you must explicitly trust this header by calling `trustProxyData()`:


```php
<?php

Request::trustProxyData();

if ($myIp == $request->getClientIp(true)) {
    // the client is a known one, so give it some more privilege
}

```

So, the `getClientIp()` method works securely in all circumstances. You can
use it in all your projects, whatever the configuration is, it will behave
correctly and safely. That's one of the goal of using a framework. If you were
to write a framework from scratch, you would have to think about all these
cases by yourself. Why not using a technology that already works?

{{< notice note >}}
If you want to learn more about the HttpFoundation component, you can have a
look at the [API](http://api.symfony.com/2.0/Symfony/Component/HttpFoundation.html) or read its dedicated [documentation](http://symfony.com/doc/current/components/http_foundation.html) on the Symfony
website.
{{< /notice >}}

Believe or not but we have our first framework. You can stop now if you want.
Using just the Symfony2 HttpFoundation component already allows you to write
better and more testable code. It also allows you to write code faster as many
day-to-day problems have already been solved for you.

As a matter of fact, projects like Drupal have adopted (for the upcoming
version 8) the HttpFoundation component; if it works for them, it will
probably work for you. Don't reinvent the wheel.

I've almost forgot to talk about one added benefit: using the HttpFoundation
component is the start of better interoperability between all frameworks and
applications using it (as of today Symfony2, Drupal 8, phpBB 4, Silex,
Midgard CMS, Zikula, ...).



