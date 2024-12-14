---
date: '2009-04-01'
title: 'Symfony Service Container: Using XML or YAML to describe Services'
summary: |
    With the last article on Dependency Injection, you learned how to describe services with PHP code by
    using the sfServiceContainerBuilder class. Today, with the help of service loaders and dumpers, you will learn how to use XML or YAML to describe your services.
params:
    author: Fabien Potencier
url: /symfony-service-container-using-xml-or-yaml-to-describe-services.html
aliases:
  - /symfony-service-container-using-xml-or-yaml-to-describe-services
  - /article/15/symfony-service-container-using-xml-or-yaml-to-describe-services
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

With the [last article](http://fabien.potencier.org/article/14/symfony-service-container-using-a-builder-to-create-services)
on Dependency Injection, you learned how to describe services with PHP code by
using the `sfServiceContainerBuilder` class. Today, with the help of
service loaders and dumpers, you will learn how to use XML or YAML to describe
your services.

{{< notice note >}}
The Subversion repository has been updated with the code needed
for today's tutorial. If you got the code from yesterday's repository,
ou can just update it (`svn up`). If not, the repository is available at
`http://svn.symfony-project.com/components/dependency_injection/trunk/`.
{{< /notice >}}

The Symfony Dependency Injection component provides helper classes that load
services using **"loader objects"**. By default, the component comes with two
of them: `sfServiceContainerLoaderFileXml` to load XML files, and
`sfServiceContainerLoaderFileYaml` to load YAML files.

But before diving into the XML and YAML notations, let's first have a look at
another part of the Symfony Dependency Injection component: the **"dumper
objects"**. A service dumper takes a container object and convert it to
another format. And of course, the component comes bundled with dumpers for
the XML and YAML formats.

To introduce the XML format, let's convert yesterday's container service
definitions to a `container.xml` file by using the
`sfServiceContainerDumperXml` dumper class.

Remember the code we used to define the `Zend_Mail` service?


```php
require_once '/PATH/TO/sfServiceContainerAutoloader.php';
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

To convert this container to an XML representation, use the following code:


```php
$dumper = new sfServiceContainerDumperXml($sc);

file_put_contents('/somewhere/container.xml', $dumper->dump());

```

A dumper class constructor takes a service container builder object as its
first argument and the `dump()` method introspects the container services and
converts them to another representation. If everything went fine, the
`container.xml` file should look like the following XML snippet:


```xml
&lt;?xml version="1.0" ?&gt;

&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;parameters&gt;
    &lt;parameter key="mailer.username"&gt;foo&lt;/parameter&gt;
    &lt;parameter key="mailer.password"&gt;bar&lt;/parameter&gt;
    &lt;parameter key="mailer.class"&gt;Zend_Mail&lt;/parameter&gt;
  &lt;/parameters&gt;
  &lt;services&gt;
    &lt;service id="mail.transport" class="Zend_Mail_Transport_Smtp" shared="false"&gt;
      &lt;argument&gt;smtp.gmail.com&lt;/argument&gt;
      &lt;argument type="collection"&gt;
        &lt;argument key="auth"&gt;login&lt;/argument&gt;
        &lt;argument key="username"&gt;%mailer.username%&lt;/argument&gt;
        &lt;argument key="password"&gt;%mailer.password%&lt;/argument&gt;
        &lt;argument key="ssl"&gt;ssl&lt;/argument&gt;
        &lt;argument key="port"&gt;465&lt;/argument&gt;
      &lt;/argument&gt;
    &lt;/service&gt;
    &lt;service id="mailer" class="%mailer.class%"&gt;
      &lt;call method="setDefaultTransport"&gt;
        &lt;argument type="service" id="mail.transport" /&gt;
      &lt;/call&gt;
    &lt;/service&gt;
  &lt;/services&gt;
&lt;/container&gt;

```

{{< notice tip >}}
The XML format supports anonymous services. An anonymous service is a
service that does not need a name and is defined directly in its use
context.  It can be very convenient when you need a service that won't
be used outside of another one scope:

     [xml]
     &lt;service id="mailer" class="%mailer.class%"&gt;
       &lt;call method="setDefaultTransport"&gt;
         &lt;argument type="service"&gt;
           &lt;service class="Zend_Mail_Transport_Smtp"&gt;
             &lt;argument&gt;smtp.gmail.com&lt;/argument&gt;
             &lt;argument type="collection"&gt;
               &lt;argument key="auth"&gt;login&lt;/argument&gt;
               &lt;argument key="username"&gt;%mailer.username%&lt;/argument&gt;
               &lt;argument key="password"&gt;%mailer.password%&lt;/argument&gt;
               &lt;argument key="ssl"&gt;ssl&lt;/argument&gt;
               &lt;argument key="port"&gt;465&lt;/argument&gt;
             &lt;/argument&gt;
           &lt;/service&gt;
         &lt;/argument&gt;
       &lt;/call&gt;
     &lt;/service&gt;
{{< /notice >}}

Loading back the container is dead simple thanks to the XML service loader
class:


```php
require_once '/PATH/TO/sfServiceContainerAutoloader.php';
sfServiceContainerAutoloader::register();

$sc = new sfServiceContainerBuilder();

$loader = new sfServiceContainerLoaderFileXml($sc);
$loader->load('/somewhere/container.xml');

```

As for dumpers, a loader takes a service container builder as its constructor
first argument, and the `load()` method reads the file and registers the
services into the container. The container is then useable as usual.

If you change the dumper code to use the `sfServiceContainerDumperYaml` class
instead, you will have a YAML representation of your services:


```php
require_once '/PATH/TO/sfYaml.php';

$dumper = new sfServiceContainerDumperYaml($sc);

file_put_contents('/somewhere/container.yml', $dumper->dump());

```

{{< notice note >}}
This will only work if you first load the sfYAML component
(`http://svn.symfony-project.com/components/yaml/trunk/`) as it is
required for the service container loader and dumper.
{{< /notice >}}

The previous container is represented like follows in YAML:


```yml
parameters:
  mailer.username: foo
  mailer.password: bar
  mailer.class:    Zend_Mail

services:
  mail.transport:
    class:     Zend_Mail_Transport_Smtp
    arguments: [smtp.gmail.com, { auth: login, username: %mailer.username%, password: %mailer.password%, ssl: ssl, port: 465 }]
    shared:    false
  mailer:
    class: %mailer.class%
    calls:
      - [setDefaultTransport, [@mail.transport]]

```

{{< notice sidebar >}}

Using the XML format gives you several advantages over the YAML one:

 * When a XML file is loaded, it is automatically validated with the
 built-in `services.xsd` file;

 * The XML can be auto-completed in IDEs;

 * The XML format is faster than the YAML one;

 * The XML format as no external dependencies (the YAML format relies
 on the sfYAML component).
{{< /notice >}}

You can of course mix and match the loaders and the dumpers to convert any
format to any other one:


```php
// Convert an XML container service definitions file to a YAML one
$sc = new sfServiceContainerBuilder();

$loader = new sfServiceContainerLoaderFileXml($sc);
$loader->load('/somewhere/container.xml');

$dumper = new sfServiceContainerDumperYaml($sc);
file_put_contents('/somewhere/container.yml', $dumper->dump());

```

{{< notice tip >}}
To keep this article short, I won't list all possibilities of the YAML
or XML format. But you can easily learn them by converting an existing
container and look at the output.
{{< /notice >}}

Using YAML or XML files for configuring your services allows you to create
your services with a GUI (yet to be done...). But it also opens a lot more
interesting possibilities.

One of the most important one is the ability to import other "resources". A
resource can be any other configuration file:


```xml
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;imports&gt;
    &lt;import resource="default.xml" /&gt;
  &lt;/imports&gt;
  &lt;parameters&gt;
    &lt;!-- These parameters override the one defined in default.xml --&gt;
  &lt;/parameters&gt;
  &lt;services&gt;
    &lt;!-- These service definitions override the one defined in default.xml --&gt;
  &lt;/services&gt;
&lt;/container&gt;

```

The `imports` section lists resources that need to be included before the
other sections are evaluated. By default, it looks for files with a path
relative to the current file, but you can also pass an array of paths to look
in as the second argument of the loader:


```php
$loader = new sfServiceContainerLoaderFileXml($sc, array('/another/path'));
$loader->load('/somewhere/container.xml');

```

You can even embed a YAML definition file in an XML one by defining the
`class` that is able to load the resource:


```xml
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;imports&gt;
    &lt;import resource="default.yml" class="sfServiceContainerLoaderFileYaml" /&gt;
  &lt;/imports&gt;
&lt;/container&gt;

```

And of course, the same goes for the YAML format:


```yml
imports:
  - { resource: default.xml, class: sfServiceContainerLoaderFileXml }

```

The `import` facility gives you a flexible way to organize your service
definition files. It is also a great way to share and reuse definition files.
Let's talk about the web session example we introduced in the first article.
When you use web sessions in a test environment, the session storage object
probably need to be mocked; on the contrary, and if you have several
load-balanced web servers, the production environment need to store its
sessions in a database like MySQL. One way to have a different configuration
based on the environment is to create several different configuration files
and import them as needed:


```xml
&lt;!-- in /framework/config/default/session.xml --&gt;
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;parameters&gt;
    &lt;parameter key="session.class"&gt;sfSessionStorage&lt;/parameter&gt;
  &lt;/parameters&gt;

  &lt;!-- service definitions go here --&gt;
&lt;/container&gt;

&lt;!-- in /project/config/session_test.xml --&gt;
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;imports&gt;
    &lt;import resource="session.xml" /&gt;
  &lt;/imports&gt;

  &lt;parameters&gt;
    &lt;parameter key="session.class"&gt;sfSessionTestStorage&lt;/parameter&gt;
  &lt;/parameters&gt;
&lt;/container&gt;

&lt;!-- in /project/config/session_prod.xml --&gt;
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;imports&gt;
    &lt;import resource="session.xml" /&gt;
  &lt;/imports&gt;

  &lt;parameters&gt;
    &lt;parameter key="session.class"&gt;sfMySQLSessionStorage&lt;/parameter&gt;
  &lt;/parameters&gt;
&lt;/container&gt;

```

Using the right configuration is trivial:


```php
$loader = new sfServiceContainerLoaderFileXml($sc, array(
  '/framework/config/default/',
  '/project/config/',
));
$loader->load('/somewhere/session_'.$environment.'.xml');

```

I can hear people crying about using XML to define the configuration, as XML
is probably not the most readable configuration format on earth. Coming from a
Symfony background, you could have written all the files in the YAML format.
But you can also decouple the service definitions from their configuration. As
you can import files form other ones, you can define services in a
`services.xml` file, and store the related configuration in a `parameters.xml`
one. You can also define parameters in a YAML file (`parameters.yml`).
Eventually, there is a last built-in INI loader that is able to read
parameters from a standard INI file:


```xml
&lt;!-- in /project/config/session_test.xml --&gt;
&lt;container xmlns="http://symfony-project.org/2.0/container"&gt;
  &lt;imports&gt;
    &lt;import resource="config.ini" class="sfServiceContainerLoaderFileIni" /&gt;
  &lt;/imports&gt;
&lt;/container&gt;

&lt;!-- /project/config/config.ini --&gt;
[parameters]
session.class = sfSessionTestStorage

```

{{< notice note >}}
It is not possible to define services in an INI file; only parameters
can be defined and parsed.
{{< /notice >}}

These examples barely scratches the surface of the container loaders and
dumpers features, but I hope this article has been a good overview of the
power of the XML and YAML formats over the PHP one. And for those who are
sceptic about the performance of a container that needs to load several files
to be configured, I think you will be blown away by the next article. As this
will be the last installment of this series on Dependency Injection, I will
also talk about a nice way to visualize your service dependencies.



