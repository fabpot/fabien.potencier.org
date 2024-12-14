---
date: '2011-10-25'
title: What is Symfony2?
summary: |
    Last week, I held a keynote presentation about Symfony2 at the Symfony
    Day conference in Cologne. Many people asked me for
    the slides, but they were quite empty and not that useful. This blog post is
    more or less what I've said during the first part of the talk.
params:
    author: Fabien Potencier
url: /what-is-symfony2.html
aliases:
    - /what-is-symfony2
    - /article/49/what-is-symfony2
---

Last week, I held a keynote presentation about Symfony2 at the Symfony
[Day](http://symfonyday.com) conference in Cologne. Many people asked me for
the slides, but they were quite empty and not that useful. This blog post is
more or less what I've said during the first part of the talk.

What is Symfony2?
-----------------

When I ask people what Symfony2 is for them, most of them say something along
the lines of: Symfony2 is a full-stack web framework written in PHP. Some also
add that this is an MVC framework. And some others add that this is a
decoupled framework. This is all fine and correct. But my definition is a bit
different. Let me tell you what it is and why I think it matters.

Symfony2 is really about two different things.

First, *Symfony2 is a reusable set of standalone, decoupled, and cohesive PHP
components that solve common web development problems*.

Then, based on these components, *Symfony2 is also a full-stack web
framework*.

Depending on your project and depending on your needs, you can either pick and
choose some of the Symfony2 components and start your project with them, or
you can use the full-stack framework and benefit from the tight integration it
provides out of the box. And choosing between the two different approaches is
really up to you.

Is Symfony2 an MVC framework?
-----------------------------

If you look around, every single framework seems to implement the MVC pattern.
And most of them are advertised as MVC frameworks... but not Symfony2. Have a
look at the documentation, and you will see that the MVC pattern is only
mentioned once or twice, but Symfony2 is never defined as being an MVC
framework. Why?

Because I really don't care whether Symfony2 is MVC or not. Probably because
the MVC word is so overloaded and because nobody implements exactly the same
MVC pattern anyway. The *separation of concerns* is all I care about. And if
you like to call Symfony2 an MVC framework, then you should know that Symfony2
is really about providing the tools for the Controller part, the View part,
but not the Model part. It's up to you to create your model by hand or use any
other tool, like an ORM. Of course, tight integration exists for the most well
known ORMs like Doctrine2 and Propel; but they are optional dependencies. The
Symfony2 core features do not and will never rely on any ORM.

I don't like MVC because that's not how the web works. *Symfony2 is an HTTP
framework*; it is *a Request/Response framework*. That's the big deal. The
fundamental principles of Symfony2 are centered around the HTTP specification.

I don't like MVC because the web has evolved a lot in the recent years and
some projects are much different than projects we had some years ago.
Sometimes, you just need a way to create a REST API. Sometimes, the logic is
mostly in the browser and the server is just used to serve data (think
backbone.js for instance). And for these projects, you don't need an MVC
framework. You need something that handles a Request and returns a Response.
You need a framework that implements the HTTP specification. HTTP streaming is
yet another example that does not fit well with the MVC pattern.

Why does it matter?
-------------------

We need to better advertise Symfony2 as a set of reusable components because I
strongly believe that the community growth in the coming years will mainly
come from the adoption of the components.

And Symfony2 adoption is all about how we sell the project. But it's also
about the perception of the project people have. And as symfony 1.0 was mostly
a monolithic framework, a lot of people still think that Symfony is indeed a
monolithic framework. Hopefully, with the release of Symfony2, the perception
is a bit different now, but it will definitely take a lot of time to change
people minds.

It matters because even if new frameworks are created every single day, I
think that in the "enterprise", there is room for only 2 or maybe 3 PHP
frameworks. The question is: how will we win the framework <del>war</del> competition?

First, we need to lead the pack technically by constantly innovating. Getting
inspiration from other technologies, other frameworks, and other web languages
and adapting some of their concepts to PHP is a key factor here. Acknowledging
that the web evolves is also very important. And Symfony2 is probably a great
step forward in this direction with many great innovations like bundles, HTTP
caching, distributions, dependency injection, templating engines, annotation
configuration, asset management, the stable API, the web profiler, and a bunch
more.

We will win the framework <del>war</del> competition if Symfony2 is everywhere. And a full-stack
framework will never become a standard in the PHP world. So, we need to find
another way.

Being everywhere is about sharing. First, by not reinventing the wheel
ourselves and by integrating existing third-party products whenever possible
(think Monolog, Composer, Doctrine, Propel, Assetic, Twig, Swiftmailer, and a
few others); but at the same time we want to be able to share our work with
others; and that's where the components come into play.

One of the Symfony2 goals is to provide building blocks for other projects.
But what do I mean when I say projects? Who are the target users for Symfony2?
Everybody: from personal projects to commercial ones; and Open-Source ones.
Personal projects and Open-Source ones are the new targets.

Silex is probably a great framework for beginners, small or personal projects,
or even for small commercial ones. Symfony2 is a great framework for bigger
projects where tens of developers are involved and where the business logic is
more complex. And the Symfony2 components are for people who don't want to use
a framework, or for other Open-Source projects that don't want to reinvent the
wheel.

To sum up, Symfony2 aims to provide the low-level architecture of PHP
projects.

Why Symfony2?
-------------

Why do I think that the Symfony2 components are in a good position to become
the low-level architecture of the PHP world?

The code is rock solid. The major Symfony2 components is the result of many
years of work and the contributions of many developers.

If you have a look at the code, you will see the `@api` tag on some classes
and methods. It indicates the public stable API. This tag means that a method
(its name, its arguments, its return value) won't change in any Symfony2 minor
versions. If you are only using the stable API, your code will not need to be
upgraded when you upgrade to a newer version of Symfony2. That's a great
selling point.

Last, but not the least, we try to be as secure as possible. We provide many
security features in the components and we also take code security very
seriously. And thanks to our great community, we have been able to conduct a
security audit from a professional company. That's something that is obviously
not possible for smaller projects.

Here are some examples of software and libraries that are currently using some
of the Symfony2 Components:

 * *Silex*: BrowerKit, CssSelector, DomCrawler, EventDispatcher,
   HttpFoundation, HttpKernel, Routing, Form, Translation, Validator

 * *Goutte*: BrowserKit, DomCrawler, CssSelector, Process, ClassLoader, Finder

 * *Behat*: Console, DependencyInjection, EventDispatcher, Finder, Yaml,
   Config, Translation

 * *Assetic*: Process

 * *Doctrine2*: Console, Yaml

 * *Propel2*: Console, ClassLoader, Yaml

 * *PHPUnit*: Yaml

 * *FLOW3*: Yaml

 * *Midguard CMS*: most of them in their next version?

 * *phpBB 4*: most of them?

 * Drupal 8*: ClassLoader, HttpFoundation, HttpKernel?

The Symfony2 Components
-----------------------

Let's see what those components can do for you. As of today, we have 21 of
them and any of them can be used as a standalone library:

 * DependencyInjection
 * EventDispatcher
 * HttpFoundation
 * DomCrawler
 * ClassLoader
 * CssSelector
 * HttpKernel
 * BrowserKit
 * Templating
 * Translation
 * Serializer
 * Validator
 * Security
 * Routing
 * Console
 * Process
 * Config
 * Finder
 * Locale
 * Yaml
 * Form

Each component has its own [Git](https://github.com/symfony) repository. You
can get the code directly from there, or you can download an archive, or
install it with PEAR, or even install it with the upcoming Composer installer
(Composer is the new PHP installer that the Symfony community started to work
on some months ago and it will be heavily used in Symfony projects to install
all their dependencies).

In the second part of this post, I will try to demonstrate the power of some
of the components with as little code as possible.

### ClassLoader

The ClassLoader component provides an autoloader that implements the PSR-0
standard (which is a standard way to autoload namespaced classes as available
in PHP 5.3). It is also able to load classes that use the PEAR naming
convention. It is really flexible as it can look for classes in different
directories based on a sub-namespace. You can even give more than one
directory for one namespace:


```php
require_once __DIR__.'/src/Symfony/Component/ClassLoader/UniversalClassLoader.php';

use Symfony\Component\ClassLoader\UniversalClassLoader;

$loader = new UniversalClassLoader();
$loader->registerNamespaces(array(
    'Symfony'          => array(__DIR__.'/src', __DIR__.'/symfony/src'),
    'Doctrine\\Common' => __DIR__.'/vendor/doctrine-common/lib',
    'Doctrine\\DBAL'   => __DIR__.'/vendor/doctrine-dbal/lib',
    'Doctrine'         => __DIR__.'/vendor/doctrine/lib',
    'Monolog'          => __DIR__.'/vendor/monolog/src',
));
$loader->registerPrefixes(array(
    'Twig_' => __DIR__.'/vendor/twig/lib',
));
$loader->register();

```

Most of the time, the Symfony2 ClassLoader is all you need to autoload all
your project classes. And for better performance, you can use an APC cached
version of the universal class loader.

### Console

Even if we are talking about a web framework, having some tools to manage your
project from the command line is nice. In Symfony2, we use the console to
generate CRUDs, update the database schema, etc. It's not required, but it is
really convenient and it can boost your productivity a lot.

This example shows how to create a command line tool very easily:


```php
use Symfony\Component\Console\Application;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;

$console = new Application();
$console
    ->register('ls')
    ->setDefinition(array(
        new InputArgument('dir', InputArgument::REQUIRED, 'Directory name'),
    ))
    ->setDescription('Displays the files in the given directory')
    ->setCode(function (InputInterface $input, OutputInterface $output) {
        $dir = $input->getArgument('dir');

        $output->writeln(sprintf('Dir listing for &lt;info&lt;%s&lt;/info&lt;', $dir));
    })
;
$console->run();

```

With only 10 lines of code or so, you have access to a lot of features like
output coloring, input and output abstractions (so that you can easily
unit-test your commands), validation, automatic help messages, and a lot more.
It's really powerful.

### YAML

YAML is a great configuration format. It's the most popular Symfony2 component
right now, because this is probably the only plain-PHP library that implements
most of the YAML 1.2 specification:


```php
use Symfony\Component\Yaml\Yaml;

$array = Yaml::parse($file);

print Yaml::dump($array);

```

### Finder

The Finder provides a very convenient and nice fluent interface to find files
and directories on the filesystem:


```php
use Symfony\Component\Finder\Finder;

$finder = new Finder();

$iterator = $finder
  ->files()
  ->name('*.php')
  ->depth(0)
  ->size('>= 1K')
  ->in(__DIR__);

foreach ($iterator as $file) {
    print $file->getRealpath()."\n";
}

```

But you can also use it to find files stored remotely like in this example
where we are looking for files on Amazon S3:


```php
$s3 = new \Zend_Service_Amazon_S3($key, $secret);
$s3->registerStreamWrapper("s3");

$finder = new Finder();
$finder->name('photos*')->size('< 100K')->date('since 1 hour ago');
foreach ($finder->in('s3://bucket-name') as $file) {
    print $file->getFilename()."\n";
}

```

### Process

The Process component allows you to execute a command in a sub-process. In
this example, I run a simple directory listing and get the result back:


```php
use Symfony\Component\Process\Process;

$process = new Process('ls -lsa');
$process->setTimeout(3600);
$process->run();
if (!$process->isSuccessful()) {
    throw new RuntimeException($process->getErrorOutput());
}

print $process->getOutput();

```

You can think that this is easy to achieve with plain PHP but it's not
especially if you want to take care of the subtle differences between the
different platforms.

And if you want to be able to get some feedback in real-time, just pass an
anonymous function to the `run()` method and you will get the output buffer as
it becomes available:


```php
use Symfony\Component\Process\Process;

$process = new Process('ls -lsa');
$process->run(function ($type, $buffer) {
    if ('err' === $type) {
        echo 'ERR > '.$buffer;
    } else {
        echo 'OUT > '.$buffer;
    }
});

```

That's great if you want to execute a long running command (like rsync-ing
files to a remote server) and give feedback to the user in real-time.

### DomCrawler

If you are familiar with jQuery, DomCrawler is a PHP equivalent. It allows you
to navigate the DOM of an HTML or XML document:


```php
use Symfony\Component\DomCrawler\Crawler;

$crawler = new Crawler();
$crawler->addContent('&lt;html&gt;&lt;body&gt;&lt;p&gt;Hello World!&lt;/p&gt;&lt;/body&gt;&lt;/html&gt;');

print $crawler->filterXPath('descendant-or-self::body/p')->text();

```

### CssSelector

In the previous example, XPath is used to get access to the DOM. Using CSS
selectors instead is far easier and so, we also have a CssSelector component.
Its only goal is to convert a CSS selector to its XPath equivalent:


```php
use Symfony\Component\CssSelector\CssSelector;

print CssSelector::toXPath('div.item > h4 > a');

```

That way, you can just use CSS Selectors with the DomCrawler instead of XPath:


```php
use Symfony\Component\DomCrawler\Crawler;

$crawler = new Crawler();
$crawler->addContent('&lt;html&gt;&lt;body&gt;&lt;p&gt;Hello World!&lt;/p&gt;&lt;/body&gt;&lt;/html&gt;');

print $crawler->filter('body > p')->text();

```

By the way, that's one example of a component (DomCrawler) that relies on
another one (CssSelector) for some optional features.

### HttpFoundation

The Symfony2 HttpFoundation component adds an object-oriented layer on top of
PHP for everything related to the Web: Requests, Responses, Uploaded files,
Cookies, Sessions, ...

In this example, we get a Request object from the current PHP global
variables:


```php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

$request = Request::createFromGlobals();
echo $request->getPathInfo();

```

You can also create a Request directly -- that's interesting for unit testing:


```php
$request = Request::create('/?foo=bar', 'GET');
echo $request->getPathInfo();

```

And here is how to create and send a Response:


```php
$response = new Response('Not Found', 404, array('Content-Type' => 'text/plain'));
$response->send();

```

The Request and the Response classes have many other methods that implement
the HTTP specification.

### Routing

The Routing component is a way to associate a Request with the code that will
convert it somehow to a Response. The example below demonstrates that with
only 10 lines of code, you can set up a fully working routing system:


```php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\Routing\Matcher\UrlMatcher;
use Symfony\Component\Routing\RequestContext;
use Symfony\Component\Routing\RouteCollection;
use Symfony\Component\Routing\Route;

$routes = new RouteCollection();
$routes->add('hello', new Route('/hello', array('controller' => 'foo')));

$context = new RequestContext();

// this is optional and can be done without a Request instance
$context->fromRequest(Request::createFromGlobals());

$matcher = new UrlMatcher($routes, $context);

$parameters = $matcher->match('/hello');

```

### EventDispatcher


```php
use Symfony\Component\EventDispatcher\EventDispatcher;
use Symfony\Component\EventDispatcher\Event;

$dispatcher = new EventDispatcher();

$dispatcher->addListener('event_name', function (Event $event) {
    // ...
});

$dispatcher->dispatch('event_name');

```

### DependencyInjection

Even the DependencyInjection component is really easy. It has many features,
but its core is simple to use. See how easy it is to configure a service that
relies on another service:


```php
use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Reference;

$sc = new ContainerBuilder();
$sc
    ->register('foo', '%foo.class%')
    ->addArgument(new Reference('bar'))
;
$sc->setParameter('foo.class', 'Foo');

$sc->get('foo');

```

### HttpKernel

The HttpKernel component provides the *dynamic* part of the HTTP
specification. It provides the HttpKernelInterface, which is the core
interface of the Symfony2 full-stack framework:


```php
interface HttpKernelInterface
{
    /**
     * Handles a Request to convert it to a Response.
     *
     * @param  Request $request A Request instance
     *
     * @return Response A Response instance
     */
    function handle(Request $request, $type = self::MASTER_REQUEST, $catch = true);
}

```

It takes a Request as an input and should return a Response as an output.
Using this interface makes your code compatible with all frameworks using the
Symfony2 components. And this will gives you many cool features for free.

Creating a framework based on the Symfony2 components is really easy. Here is
a very simple, but fully-featured framework based on the Symfony2 components:


```php
$routes = new RouteCollection();
$routes->add('hello', new Route('/hello', array('_controller' =>
    function (Request $request) {
        return new Response(sprintf("Hello %s", $request->get('name')));
    }
)));

$request = Request::createFromGlobals();

$context = new RequestContext();
$context->fromRequest($request);

$matcher = new UrlMatcher($routes, $context);

$dispatcher = new EventDispatcher();
$dispatcher->addSubscriber(new RouterListener($matcher));

$resolver = new ControllerResolver();

$kernel = new HttpKernel($dispatcher, $resolver);

$kernel->handle($request)->send();

```

This is all you need to create a flexible framework with the Symfony2
components.

Want to add an HTTP reverse proxy and benefit from HTTP caching and Edge Side
Includes?


```php
$kernel = new HttpKernel($dispatcher, $resolver); 

$kernel = new HttpCache($kernel, new Store(__DIR__.'/cache'));

```

Want to functional test this small framework?


```php
$client = new Client($kernel);
$crawler = $client->request('GET', '/hello/Fabien');

$this->assertEquals('Fabien', $crawler->filter('p > span')->text());

```

Want nice error pages instead of ugly PHP exceptions?


```php
$dispatcher->addSubscriber(new ExceptionListener(function (Request $request) {
    $msg = 'Something went wrong! ('.$request->get('exception')->getMessage().')';

    return new Response($msg, 500);
}));

```

I can continue on and on but I think you get the point.

And that's why the simple looking HttpKernelInterface is so powerful. It gives
you access to a lot of cool features, ready to be used out of the box, with no
efforts.

To sum up, the Symfony2 components are reusable PHP 5.3 libraries that can be
used standalone. They are highly configurable, tested, and secured. And you
should consider using them even if you don't need or don't want to use a
full-stack framework for your next project.



