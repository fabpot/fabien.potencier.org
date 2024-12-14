---
date: '2009-03-31'
title: 'Symfony Service Container: Using a Builder to create Services'
summary: |
    In the previous article
    on Dependency Injection, you learned how to use the sfServiceContainer class
    to provide a more appealing interface to your service containers. In this
    article, we will go one step further and learn how to leverage the
    sfServiceContainerBuilder class to describe services and their configuration
    in pure PHP code.
params:
    author: Fabien Potencier
url: /symfony-service-container-using-a-builder-to-create-services.html
aliases:
    - /article/14/symfony-service-container-using-a-builder-to-create-services
    - /symfony-service-container-using-a-builder-to-create-services
---

{{< notice note >}}
This article is part of a series on Dependency Injection in general and on a
lightweight implementation of a Container in PHP in particular:

 * [Part 1: What is Dependency Injection? ](http://fabien.potencier.org/article/11/what-is-dependency-injection)
 * [Part 2: Do you need a Dependency Injection Container? ](http://fabien.potencier.org/article/12/do-you-need-a-dependency-injection-container)
 * [Part 3: Introduction to the Symfony Service Container](http://fabien.potencier.org/article/13/introduction-to-the-symfony-service-container)
 * [Part 4: Symfony Service Container: Using a Builder to create Services](http://fabien.potencier.org/article/14/symfony-service-container-using-a-builder-to-create-services)
 * [Part 5: Symfony Service Container: Using XML or YAML to describe Services](http://fabien.potencier.org/article/15/symfony-service-container-using-xml-or-yaml-to-describe-services)
 * [Part 6: The Need for Speed](http://fabien.potencier.org/article/16/symfony-service-container-the-need-for-speed)
{{< /notice >}}

In the [previous article](http://fabien.potencier.org/article/13/introduction-to-the-symfony-service-container)
on Dependency Injection, you learned how to use the `sfServiceContainer` class
to provide a more appealing interface to your service containers. In this
article, we will go one step further and learn how to leverage the
`sfServiceContainerBuilder` class to describe services and their configuration
in pure PHP code.

{{< notice note >}}
The Subversion repository has been updated with the code needed for this tutorial. If you have
checkout the code yesterday, you can simple update it. If not, the repository is available at `http://svn.symfony-project.com/components/dependency_injection/trunk/`.
{{< /notice >}}

The `sfServiceContainerBuilder` class extends the basic `sfServiceContainer`
class and allows the developer to describe services with a simple PHP
interface.

{{< notice sidebar >}}
The Service Container Interface

All service container classes share the same interface, defined in
`sfServiceContainerInterface`:

     [php]
     interface sfServiceContainerInterface
     {
       public function setParameters(array $parameters);
       public function addParameters(array $parameters);
       public function getParameters();
       public function getParameter($name);
       public function setParameter($name, $value);
       public function hasParameter($name);
       public function setService($id, $service);
       public function getService($id);
       public function hasService($name);
     }
{{< /notice >}}

The description of the services are done by registering service definitions.
Each service definition describes a service: from the class to use to the
arguments to pass to the constructor, and a bunch of other configuration
properties (see the `sfServiceDefinition` sidebar below).

The `Zend_Mail` example can easily be rewritten by removing all the hardcoded
code and building it dynamically with the builder class instead:


```php
require_once 'PATH/TO/sf/lib/sfServiceContainerAutoloader.php';
sfServiceContainerAutoloader::register();

$sc = new sfServiceContainerBuilder();

$sc->
  register('mail.transport', 'Zend_Mail_Transport_Smtp')->
  addArgument('smtp.gmail.com')->
  addArgument(array(
    'auth'     => 'login',
    'username' => '%mailer.username%',
    'password' => '%mailer.password%',
    'ssl'      => 'ssl',
    'port'     => 465,
  ))->
  setShared(false)
;

$sc->
  register('mailer', '%mailer.class%')->
  addMethodCall('setDefaultTransport', array(new sfServiceReference('mail.transport')))
;

```

The creation of a service is done by calling the `register()` method, which
takes the service name and the class name, and returns a `sfServiceDefinition`
instance.

{{< notice tip >}}
A service definition is internally represented by an object of
class `sfServiceDefinition`. It is also possible to create one by
hand and registering it directly by using the service container
`setServiceDefinition()` method.
{{< /notice >}}

The definition object implements a fluid interface and provides
methods that configure the service. In the above example, we have used the
following ones:

  * `addArgument()`: Adds an argument to pass to the service constructor.

  * `setShared()`: Whether the service must be unique for a container or not
    (`true` by default).

  * `addMethodCall()`: A method to call after the service has been created.
    The second argument is an array of arguments to pass to the method.

Referencing a service is now done with a `sfServiceReference` instance. This
special object is dynamically replaced with the actual service when the
referencing service is created.

During the registration phase, no service is actually created, it is just
about the description of the services. The services are only created when you
actually want to work with them. It means you can register the services in any
order without taking care of the dependencies between them. It also means you
can override an existing service definition by re-registering a service with
the same name. That's yet another simple way to override a service for testing
purpose.

{{< notice sidebar >}}
The `sfServiceDefinition` Class

A service has several properties that changes the way it is created and
configured:

 * `setConstructor()`: Sets the static method to use when the service
 is created, instead of the standard `new` construct (useful for
 factories).

 * `setClass()`: Sets the service class.

 * `setArguments()`: Sets the arguments to pass to the constructor (the
 order is of course significant).

 * `addArgument()`: Adds an argument for the constructor.

 * `setMethodCalls()`: Sets the service methods to call after
 service creation. These methods are called in the same order as the
 registration.

 * `addMethodCall()`: Adds a service method call to call after service
 creation. You can add a call to the same method several times if
 needed.

 * `setFile()`: Sets a file to include before creating a service
 (useful if the service class if not autoloaded).

 * `setShared()`: Whether the service must be unique for a container or
 not (`true` by default).

 * `setConfigurator()`: Sets a PHP callable to call after the service
 has been configured.
{{< /notice >}}

As the `sfServiceContainerBuilder` class implements the standard
`sfServiceContainerInterface` interface, using the service container does not
need to be changed:


```php
$sc->addParameters(array(
  'mailer.username' => 'foo',
  'mailer.password' => 'bar',
  'mailer.class'    => 'Zend_Mail',
));

$mailer = $sc->mailer;

```

The `sfServiceContainerBuilder` is able to describe any object instantiation
and configuration. We have demonstrated it with the `Zend_Mail` class, and
here is another example using the `sfUser` class from Symfony:


```php
$sc = new sfServiceContainerBuilder(array(
  'storage.class'        => 'sfMySQLSessionStorage',
  'storage.options'      => array('database' => 'session', 'db_table' => 'session'),
  'user.class'           => 'sfUser',
  'user.default_culture' => 'en',
));

$sc->register('dispatcher', 'sfEventDispatcher');

$sc->
  register('storage', '%storage.class%')->
  addArgument('%storage.options%')
;

$sc->
  register('user', '%user.class%')->
  addArgument(new sfServiceReference('dispatcher'))->
  addArgument(new sfServiceReference('storage'))->
  addArgument(array('default_culture' => '%user.default_culture%'))->
;

$user = $sc->user;

```

{{< notice note >}}
In the Symfony example, even if the storage object takes an
array of options as an argument, we passed a string placeholder
(`addArgument('%storage.options%')`). The container is smarter enough
to actually pass an array, the value of the placeholder.
{{< /notice >}}

That's all for today. Using PHP code to describe the services is quite simple
and powerful. It gives you a tool to create your container without duplicating
too much code and to abstract objects instantiation and configuration. In the
next article, we will see how services can also be described with XML or YAML
files. Stay tuned!



