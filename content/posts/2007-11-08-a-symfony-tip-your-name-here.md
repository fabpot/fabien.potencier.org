---
date: '2007-11-08'
title: 'A symfony tip: "Your name here"'
summary: |
    Learn how to automatically customize the symfony generated classes author name.
params:
    author: Fabien Potencier
url: /a-symfony-tip-your-name-here.html
aliases:
  - /article/24/a-symfony-tip-your-name-here
  - /a-symfony-tip-your-name-here
---

When creating a module with the `init-module` task (`generate:module` in symfony 1.1),
symfony creates an `actions.class.php` file:


```php
[?php

/**
 * article actions.
 *
 * @package    symfony
 * @subpackage article
 * @author     Your name here
 * @version    SVN: $Id$
 */
class articleActions extends sfActions
{
  /**
   * Executes index action
   *
   */
  public function executeIndex()
  {
    $this->forward('default', 'module');
  }
}

```

symfony generates the class content and adds some PHPDoc comments.
But if you have a closer look, you see that the author is a meaningless `Your name here` placeholder.

You can change this default by adding an `author` property in the `config/properties.ini` file:


```ini
[symfony]
  name=aidedecamp
  author=Fabien Potencier <fabien.potencier [at] symfony-project.com>

```

symfony will now use this author when generating a module:


```php
[?php

/**
 * article actions.
 *
 * @package    symfony
 * @subpackage article
 * @author     Fabien Potencier <fabien.potencier [at] symfony-project.com>
 * @version    SVN: $Id$
 */
class articleActions extends sfActions
{
  /**
   * Executes index action
   *
   */
  public function executeIndex()
  {
    $this->forward('default', 'module');
  }
}

```

The `name` property is used for the following tasks in symfony 1.0:

  * `init-module`
  * `init-controller`
  * `init-batch`
  * `propel-generate-crud`
  * `propel-init-crud`
  * `propel-init-admin`

Here is the tasks list for symfony 1.1:

  * `generate:module`
  * `generate:controller`
  * `generate:batch`
  * `propel:generate-crud`
  * `propel:generate-admin`


