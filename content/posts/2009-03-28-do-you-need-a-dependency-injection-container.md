---
date: '2009-03-28'
title: Do you need a Dependency Injection Container?
summary: |
    In the first installment
    of this series on Dependency Injection, I have tried to give concrete web
    examples of Dependency Injection in action. Today, I will talk about
    Dependency Injection Containers.
params:
    author: Fabien Potencier
url: /do-you-need-a-dependency-injection-container.html
aliases:
  - /article/12/do-you-need-a-dependency-injection-container
  - /do-you-need-a-dependency-injection-container
---

{{< notice note >}}
This article is part of a series on Dependency Injection in general and on a
lightweight implementation of a Container in PHP in particular:

 * [Part 1: What is Dependency Injection? ](https://fabien.potencier.org/article/11/what-is-dependency-injection)
 * [Part 2: Do you need a Dependency Injection Container? ](https://fabien.potencier.org/article/12/do-you-need-a-dependency-injection-container)
 * [Part 3: Introduction to the Symfony Service Container](https://fabien.potencier.org/article/13/introduction-to-the-symfony-service-container)
 * [Part 4: Symfony Service Container: Using a Builder to create Services](https://fabien.potencier.org/article/14/symfony-service-container-using-a-builder-to-create-services)
 * [Part 5: Symfony Service Container: Using XML or YAML to describe Services](https://fabien.potencier.org/article/15/symfony-service-container-using-xml-or-yaml-to-describe-services)
 * [Part 6: The Need for Speed](https://fabien.potencier.org/article/16/symfony-service-container-the-need-for-speed)
{{< /notice >}}

In the [first installment](https://fabien.potencier.org/article/11/what-is-dependency-injection)
of this series on Dependency Injection, I have tried to give concrete web
examples of Dependency Injection in action. Today, I will talk about
Dependency Injection Containers.

First, let's start with a bold statement:

  *Most of the time, you don't need a Dependency Injection Container to
  benefit from Dependency Injection*.

But when you need to manage a lot of different objects with a lot of
dependencies, a Dependency Injection Container can be really helpful (think of
a framework for instance).

If you remember the example of the first article, creating a `User` object
required to first create a `SessionStorage` object. Not a big deal, but still,
you have to know about all the dependencies you need before creating the
object you need:


```php
$storage = new SessionStorage('SESSION_ID');
$user = new User($storage);

```

In the upcoming articles, we will talk about the PHP implementation of a
Dependency Injection Container for Symfony 2. As I want to make it clear that
the implementation is in no way bound to Symfony, I will take Zend Framework
examples to illustrate my articles.

{{< notice note >}}
Contrary to popular belief, there is no such thing as a
PHP framework war. I really appreciate the Zend Framework
components, and as a matter of fact, a lot of their
[libraries](http://www.symfony-project.org/jobeet/1_2/Doctrine/en/17)
can be really useful in a Symfony project.
{{< /notice >}}

The Zend Framework `Mail` library, which ease emails management, uses the PHP
`mail()` function by default to send emails, which is not really flexible.
Thankfully, it is quite easy to change this behavior by providing a transport
object. The following snippet of code shows how to create a `Zend_Mail` object
that sends its emails using a Gmail account:


```php
$transport = new Zend_Mail_Transport_Smtp('smtp.gmail.com', array(
  'auth'     => 'login',
  'username' => 'foo',
  'password' => 'bar',
  'ssl'      => 'ssl',
  'port'     => 465,
));

$mailer = new Zend_Mail();
$mailer->setDefaultTransport($transport);

```

{{< notice note >}}
To keep this article short enough, I use simple examples. Of
course, for these simple examples, it does not make sense to have a
container. Think of the examples as being just a small part of the
collections of objects that need to be managed by the container.
{{< /notice >}}

A Dependency Injection Container is an object that knows how to instantiate
and configure objects. And to be able to do its job, it needs to knows about
the constructor arguments and the relationships between the objects.

Here is a simple hardcoded container for the above `Zend_Mail` example:


```php
class Container
{
  public function getMailTransport()
  {
    return new Zend_Mail_Transport_Smtp('smtp.gmail.com', array(
      'auth'     => 'login',
      'username' => 'foo',
      'password' => 'bar',
      'ssl'      => 'ssl',
      'port'     => 465,
    ));
  }

  public function getMailer()
  {
    $mailer = new Zend_Mail();
    $mailer->setDefaultTransport($this->getMailTransport());

    return $mailer;
  }
}

```

Using the container class is simple enough:


```php
$container = new Container();
$mailer = $container->getMailer();

```

When using the container, we just ask for a mailer object, and we don't need
to know anything about how to create it anymore; all the knowledge about how
to create an instance of the mailer is now embedded into the container. The
mail transport dependency will be injected automatically by the container,
thanks to the `getMailTransport()` call. All the power of the container lies
in this simple call!

But, astute readers might have noticed a problem here. The container itself
has everything hardcoded! So, we need to go one step further and add
parameters to the mix to make the container really useful:


```php
class Container
{
  protected $parameters = array();

  public function __construct(array $parameters = array())
  {
    $this->parameters = $parameters;
  }

  public function getMailTransport()
  {
    return new Zend_Mail_Transport_Smtp('smtp.gmail.com', array(
      'auth'     => 'login',
      'username' => $this->parameters['mailer.username'],
      'password' => $this->parameters['mailer.password'],
      'ssl'      => 'ssl',
      'port'     => 465,
    ));
  }

  public function getMailer()
  {
    $mailer = new Zend_Mail();
    $mailer->setDefaultTransport($this->getMailTransport());

    return $mailer;
  }
}

```

It is now easy to change the Google username and password by passing some
parameters to the container constructor:


```php
$container = new Container(array(
  'mailer.username' => 'foo',
  'mailer.password' => 'bar',
));
$mailer = $container->getMailer();

```

If you need to change the mailer class for testing, the object class name can
also be passed as a parameter:


```php
class Container
{
  // ...

  public function getMailer()
  {
    $class = $this->parameters['mailer.class'];

    $mailer = new $class();
    $mailer->setDefaultTransport($this->getMailTransport());

    return $mailer;
  }
}

$container = new Container(array(
  'mailer.username' => 'foo',
  'mailer.password' => 'bar',
  'mailer.class'    => 'Zend_Mail',
));
$mailer = $container->getMailer();

```

Last, but not the least, each time I want to get a mailer, I don't need a new
instance of it. So, the container can be changed to always return the same
object:


```php
class Container
{
  static protected $shared = array();

  // ...

  public function getMailer()
  {
    if (isset(self::$shared['mailer']))
    {
      return self::$shared['mailer'];
    }

    $class = $this->parameters['mailer.class'];

    $mailer = new $class();
    $mailer->setDefaultTransport($this->getMailTransport());

    return self::$shared['mailer'] = $mailer;
  }
}

```

With the introduction of the static `$shared` property, each time you call the
`getMailer()` method, the object created for the first call will be returned.

That wraps up the basic features that need to be implemented by a Dependency
Injection Container. A Dependency Injection Container manages objects: from
their instantiation to their configuration. The objects themselves do not know
that they are managed by a container and know nothing about the container.
That's why a container is able to manage any PHP object. It is even better if
the objects use dependency injection for their dependencies, but that's not a prerequisite.

Of course, creating and maintaining the container class by hand can become a
nightmare pretty fast. But as the requirements are quite minimal for a
container to be useful, it is easy to implement one. The next installment of
this series will talk about the Symfony 2 dependency injection container
implementation.



