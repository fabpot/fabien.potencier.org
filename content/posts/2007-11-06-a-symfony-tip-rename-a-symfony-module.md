---
date: '2007-11-06'
title: 'A symfony tip: Rename a symfony Module'
summary: |
    Learn how to rename a symfony module step by step.
params:
    author: Fabien Potencier
url: /a-symfony-tip-rename-a-symfony-module.html
aliases:
  - /a-symfony-tip-rename-a-symfony-module
  - /article/23/a-symfony-tip-rename-a-symfony-module
---

Renaming a symfony module can a bit more involving than renaming an application.

Here are the steps to follow to rename a `post` symfony module in a `frontend` application to `article`:

  * The first obvious step is to rename the `apps/frontend/modules/post/` directory to `apps/frontend/modules/article/` and to rename the `postActions` class to `articleActions`.

  * The second step is to rename the functional tests for this module from `test/functional/frontend/postActionsTest.php` to `test/functional/frontend/articleActionsTest.php`.

  * If you check the executed `module` in your functional tests, you also have to change the module name for every `isRequestParameter` call:


    ```php
    $browser->
      // ...
      isRequestParameter('module', 'article')-> // <<-- change post to article here
      // ...

    ```

  * If you don't have named routes for every action in the `post` module, you have to change all `link_to()`, `url_for()`
    `form_tag()`, and `->redirect()` calls to reflect the name change:


    ```php
    [?php echo link_to('post/index') // <<-- change post to article ?]

    [?php echo form_tag('post/update') ?]

    $this->redirect('post/edit');

    ```

  * Finally, every time you do some refactoring, don't forget to launch your functional tests
    to check you haven't broken something in the process:

    
    ``` bash
    $ ./symfony test-functional
    ```
    

You're done. The old `post` module name is now `article`.


