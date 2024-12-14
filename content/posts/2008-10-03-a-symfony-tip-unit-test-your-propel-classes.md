---
date: '2008-10-03'
title: 'A symfony tip: Unit test your Propel classes'
summary: |
    Learn how to easily test your Propel classes
params:
    author: Fabien Potencier
url: /a-symfony-tip-unit-test-your-propel-classes.html
aliases:
  - /a-symfony-tip-unit-test-your-propel-classes
  - /article/28/a-symfony-tip-unit-test-your-propel-classes
---

Unit testing Propel classes in symfony 1.0 was a bit tricky. As of symfony 1.1, it is much more easier.

To test a Propel object, you need to enable the autoloading of Propel classes, provide a valid database connection to Propel, and feed the database with some test data.

Thankfully, it is quite easy as symfony already provides everything you need:

  * To get autoloading, you need to initialize a configuration object:


    ```php
    ProjectConfiguration::getApplicationConfiguration('frontend', 'test', true)

    ```

  * To get a database connection, you need to initialize the `sfDatabaseManager` class:


    ```php
    new sfDatabaseManager($configuration);

    ```

  * To load some test data, you can use the `sfPropelData` class:


    ```php
    $loader = new sfPropelData();
    $loader->loadData(sfConfig::get('sf_data_dir').'/fixtures');

    ```

The following example shows you a very simple test file for a `User` Propel model:


```php
<?php

include(dirname(__FILE__).'/../bootstrap/unit.php');

new sfDatabaseManager(ProjectConfiguration::getApplicationConfiguration('frontend', 'test', true));
$loader = new sfPropelData();
$loader->loadData(sfConfig::get('sf_data_dir').'/fixtures');

$t = new lime_test(1, new lime_output_color());

// begin testing your model class
$t->diag('->retrieveByUsername()');
$user = UserPeer::retrieveByUsername('fabien');
$t->is($user->getLastName(), 'Potencier', '->retrieveByUsername() returns the User for the given username');


```



