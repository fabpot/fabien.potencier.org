---
date: '2007-11-05'
title: 'A symfony tip: Rename a symfony Application'
summary: |
    Learn how to rename a symfony application step by step.
params:
    author: Fabien Potencier
url: /a-symfony-tip-rename-a-symfony-application.html
aliases:
    - /a-symfony-tip-rename-a-symfony-application
    - /article/22/a-symfony-tip-rename-a-symfony-application
---

Renaming a symfony application is quite simple.

Here are the steps to follow to rename a `fo` symfony application to `frontend`:

  * The first obvious step is to rename the `apps/fo/` directory to `apps/frontend/`.

  * The second step is rename the functional test directory `test/functional/fo/` to `test/functional/frontend/`.

  * You can optionally rename the front controllers from `web/fo_*.php` to `web/frontend_*.php`.

  * Finally, you have to update the front controllers.
    By default, you have two file to update: `fo_dev.php` and `fo.php` (or `index.php` if this is the main application).

    Change the `APP` constant from `fo` to `frontend`:


    ```php
    [?php

    define('SF_ROOT_DIR',    realpath(dirname(__FILE__).'/..'));
    define('SF_APP',         'frontend'); // <<-- I've changed fo to frontend here
    define('SF_ENVIRONMENT', 'dev');
    define('SF_DEBUG',       true);

    require_once(SF_ROOT_DIR.DIRECTORY_SEPARATOR.'apps'.DIRECTORY_SEPARATOR.SF_APP.DIRECTORY_SEPARATOR.'config'.DIRECTORY_SEPARATOR.'config.php');

    sfContext::getInstance()->getController()->dispatch();

    ```

You're done. The old `fo` app name is now `frontend`.

{{< notice warning >}}
If you use the symfony `sync` task to deploy your project, check that your development front controllers
are not deployed. This is the case if you have a recent default `rsync_exclude.txt` file:


```txt
 [txt]
 .svn
 /web/uploads/*
 /cache/*
 /log/*
 /web/*_dev.php # <<-- All dev front controllers won't be deployed
```

{{< /notice >}}


