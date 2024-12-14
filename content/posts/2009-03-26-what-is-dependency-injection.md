---
date: '2009-03-26'
title: What is Dependency Injection?
summary: |
    This article is the first of a series on Dependency Injection in general and
    the implementation of a Dependency Injection Container in PHP.
params:
    author: Fabien Potencier
url: /what-is-dependency-injection.html
aliases:
  - /article/11/what-is-dependency-injection
  - /what-is-dependency-injection
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

This article is the first of a series on Dependency Injection in general and
the implementation of a Dependency Injection Container in PHP.

Today, I won't talk about the container yet as I first want to introduce the
concept of Dependency Injection with some concrete examples that will
hopefully demonstrate the problems it tries to solve and the benefits it
gives to the developer. If you already knows the concept of Dependency
Injection, you can safely skip this article and instead wait for the next one.

Dependency Injection is probably one of the most dead simple design pattern I
know. And odds are you have probably already used Dependency Injection. But it
is also one of the most difficult one to explain well. I think it is partly
due to the nonsense examples used in most introductions to Dependency
Injection. I have tried to come up with examples that fits the PHP world
better. As PHP is a language mainly used for web development, let's take a
simple Web example.

To overcome the statelessness of the HTTP protocol, web applications need
a way to store user information between web requests. This is of course quite
simple to achieve by using a cookie, or even better, by using the built-in PHP
session mechanism:


```php
$_SESSION['language'] = 'fr';

```

The above code stores the user language in the `language` session variable.
So, for all subsequent requests of the same user, the `language` will be
available in the global `$_SESSION` array:


```php
$user_language = $_SESSION['language'];

```

As Dependency Injection only makes sense in an Object-Oriented world, let's
pretend we have a `SessionStorage` class that wraps the PHP session mechanism:


```php
class SessionStorage
{
  function __construct($cookieName = 'PHP_SESS_ID')
  {
    session_name($cookieName);
    session_start();
  }

  function set($key, $value)
  {
    $_SESSION[$key] = $value;
  }

  function get($key)
  {
    return $_SESSION[$key];
  }

  // ...
}

```

... and a `User` class that provides a nice high-level interface:


```php
class User
{
  protected $storage;

  function __construct()
  {
    $this->storage = new SessionStorage();
  }

  function setLanguage($language)
  {
    $this->storage->set('language', $language);
  }

  function getLanguage()
  {
    return $this->storage->get('language');
  }

  // ...
}

```

Those classes are simple enough and using the `User` class is also rather
easy:


```php
$user = new User();
$user->setLanguage('fr');
$user_language = $user->getLanguage();

```

All is good and well... until you want more flexibility. What if you want to
change the session cookie name for instance? Here are some random
possibilities:

  * Hardcode the name in the `User` class in the `SessionStorage`
    constructor:


    ```php
    class User
    {
      function __construct()
      {
        $this->storage = new SessionStorage('SESSION_ID');
      }

      // ...
    }

    ```

  * Define a constant outside of the `User` class:


    ```php
    class User
    {
      function __construct()
      {
        $this->storage = new SessionStorage(STORAGE_SESSION_NAME);
      }

      // ...
    }

    define('STORAGE_SESSION_NAME', 'SESSION_ID');

    ```

  * Add the session name as a `User` constructor argument:


    ```php
    class User
    {
      function __construct($sessionName)
      {
        $this->storage = new SessionStorage($sessionName);
      }

      // ...
    }

    $user = new User('SESSION_ID');

    ```

  * Add an array of options for the storage class:


    ```php
    class User
    {
      function __construct($storageOptions)
      {
        $this->storage = new SessionStorage($storageOptions['session_name']);
      }

      // ...
    }

    $user = new User(array('session_name' => 'SESSION_ID'));

    ```

All these alternatives are quite bad. Hardcoding the session name in the
`User` class does not really solve the problem as you cannot easily change
your mind later on without changing the `User` class again. Using a constant
is also a bad idea as the `User` class now depends on a constant to be set.
Passing the session name as an argument or as an array of options is probably
the best solution, but it still smells bad. It clutters the `User` constructor
arguments with things that are not relevant to the object itself.

But there is yet another problem that cannot be solved easily: How can I
change the `SessionStorage` class? For instance, to replace it with a mock
object to ease testing. Or perhaps because you want to store the sessions in a
database table or in memory. That's impossible with the current
implementation, except if you change the `User` class.

Enter Dependency Injection. *Instead of creating the `SessionStorage` object
inside the `User` class, let's inject the `SessionStorage` object in the
`User` object by passing it as a constructor argument*:


```php
class User
{
  function __construct($storage)
  {
    $this->storage = $storage;
  }

  // ...
}

```

That's Dependency Injection. Nothing more! Using the `User` class is now a bit
more involving as you first need to create the `SessionStorage` object:


```php
$storage = new SessionStorage('SESSION_ID');
$user = new User($storage);

```

Now, configuring the session storage object is dead simple, and replacing the
session storage class is also very easy. And everything is possible without
changing the `User` class thanks to the better separation of concerns.

The [Pico Container website](http://www.picocontainer.org/injection.html)
describes Dependency Injection like this:

  "Dependency Injection is where components are given their dependencies
  through their constructors, methods, or directly into fields."

{{< notice tip >}}
As any other design pattern, Dependency Injection
also has some anti-patterns.  The
[Pico Container website](http://www.picocontainer.org/)
describes some of them.
{{< /notice >}}

Dependency Injection is not restricted to constructor injection:

  * Constructor Injection:


    ```php
    class User
    {
      function __construct($storage)
      {
        $this->storage = $storage;
      }

      // ...
    }

    ```

  * Setter Injection:


    ```php
    class User
    {
      function setSessionStorage($storage)
      {
        $this->storage = $storage;
      }

      // ...
    }

    ```

  * Property Injection:


    ```php
    class User
    {
      public $sessionStorage;
    }

    $user->sessionStorage = $storage;

    ```

As a rule of thumb, constructor injection is best for required dependencies,
like in our example, and setter injection is best for optional dependencies,
like a cache object for instance.

Nowadays, most modern PHP frameworks heavily use Dependency Injection to
provide a set of decoupled but cohesive components:


```php
// symfony: A constructor injection example
$dispatcher = new sfEventDispatcher();
$storage = new sfMySQLSessionStorage(array('database' => 'session', 'db_table' => 'session'));
$user = new sfUser($dispatcher, $storage, array('default_culture' => 'en'));

// Zend Framework: A setter injection example
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

If you are interested in learning more about Dependency Injection, I highly
recommend you read the
[Martin Fowler introduction](http://www.martinfowler.com/articles/injection.html)
or the excellent
[Jeff More presentation](http://www.procata.com/talks/phptek-may2007-dependency.pdf).
You can also have a look at a
[presentation](http://fabien.potencier.org/talk/19/decouple-your-code-for-reusability-ipc-2008)
I gave last year on Dependency Injection, where I talk in more detail
on the example I have talked about in this article.

That's all for today. I hope you now have a better understanding of the Dependency Injection concept. In the next installment of this series, I will talk about Dependency Injection Containers.



