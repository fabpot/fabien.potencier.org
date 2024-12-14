---
date: '2008-02-17'
title: 'A symfony tip: Tweak Template Names'
summary: |
    Learn how to customize the template used by your actions.
params:
    author: Fabien Potencier
url: /a-symfony-tip-tweak-template-names.html
aliases:
  - /a-symfony-tip-tweak-template-names
  - /article/27/a-symfony-tip-tweak-template-names
---

>*WARNING*: Some parts of this tip only work with the latest symfony 1.1 version
>and only if you have updated your `.htaccess` file.

By default, symfony guesses the template name and directory based on the
executed action.

Let's take a simple `content` module with a `static` action in the `frontend`
application as an example:


```php
<?php

class contentActions extends sfActions
{
  public function executeStatic()
  {
  }
}

```

When calling the `content/static` action, symfony will load the `staticSuccess.php` file
as the template and it will look for it in the `apps/frontend/modules/content/templates/` directory.

symfony adds a `Success` suffix by default because the previous code is
equivalent to this one:


```php
<?php

class contentActions extends sfActions
{
  public function executeStatic()
  {
    return sfView::SUCCESS; // sfView::SUCCESS == 'Success'
  }
}

```

As it doesn't make sense for the `content/static` action to only manage one static file,
we need to pass the page as a request parameter to be able to select our template.
So, we add a routing rule:


```yml
page:
  url:   /static/:page
  param: { module: content, action: static }

```

Now, let's see how we can update the action to load a different template based
on the `page` request parameter.

Change the suffix
=================

The first thing we can do is to change the default `Success` suffix by
explicitly returning the `page`:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    return $request->getParameter('page');
  }
}

```

Now, if we request the `/static/about` page,
symfony will load the `staticabout.php` template.

Here is the `templates/` directory content for two different pages:

    templates/
      staticabout.php
      staticcontact.php

Change the template name
========================

Instead of changing the suffix, we can also change the template name like this:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    $this->setTemplate($request->getParameter('page'));
  }
}

```

Now, when requesting the `/static/about` page,
symfony will load the `aboutSuccess.php` template.

Here is the `templates/` directory content:

    templates/
      aboutSuccess.php
      contactSuccess.php

As the content module can have some other actions, we might want to put all the static templates
under a sub-directory. This can be done by prefixing the template name with the sub-directory:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    $this->setTemplate('static/'.$request->getParameter('page'));
  }
}

```

And symfony will now load the `static/aboutSuccess.php` template.

Here is the `templates/` directory content:

    templates/
      static/
        aboutSuccess.php
        contactSuccess.php

And if we don't want the `Success` suffix, we can return an empty string:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    $this->setTemplate('static/'.$request->getParameter('page'));

    return '';
  }
}

```

And now symfony will load the `static/about.php` template.

Here is the `templates/` directory content:

    templates/
      static/
        about.php
        contact.php

More fun with the suffix
========================

Let's add a new requirement: The static pages exist in two formats, HTML and XML,
and both formats must be available to the end user.

This means that the user can now request `/static/about.html` or `/static/about.xml`.
And by default, if the user doesn't provide any extension, the action must return
the HTML version.

Let's add the `extension` request parameter in the routing rule:


```yml
page:
  url:   /static/:page.:extension
  param: { module: content, action: static, extension: html }
  requirements:
    extension: (html|xml)

```

As an added bonus, we've added a requirement to check the correctness of the extension.

And here is the rewritten action:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    $this->setTemplate('static/'.$request->getParameter('page'));

    return '.'.$request->getParameter('extension');
  }
}

```

So, if we request the `/static/about` page (or `/static/about.hml`),
symfony will load the `static/about.html.php` template.
And if we request the XML version of the page (`/static/about.xml`), symfony will load the
`static/about.xml.php` template.

Here is the `templates/` directory content:

    templates/
      static/
        about.html.php
        contact.html.php
        about.xml.php
        contact.xml.php

We can also store our templates in sub-directories according to the format:


```php
<?php

class contentActions extends sfActions
{
  public function executePage($request)
  {
    $this->setTemplate('static/'.$request->getParameter('extension').'/'.$request->getParameter('page'));

    return '';
  }
}

```

Here is the `templates/` directory content:

    templates/
      static/
        html/
          about.php
          contact.php
        xml/
          about.php
          contact.php

In the real life, we would also have changed the response content type for the XML format.

Here is a summary of all the possibilities we have seen in this tip:


```php
// action
return $request->getParameter('page');
// template name
templates/staticabout.php

// action
$this->setTemplate($request->getParameter('page'));
// template name
templates/aboutSuccess.php

// action
$this->setTemplate('static/'.$request->getParameter('page'));
// template name
templates/static/aboutSuccess.php

// action
$this->setTemplate('static/'.$request->getParameter('page'));
return '';
// template name
templates/static/about.php

// action
$this->setTemplate('static/'.$request->getParameter('page'));
return '.'.$request->getParameter('extension');
// template name
templates/static/about.html.php

// action
$this->setTemplate('static/'.$request->getParameter('extension').'/'.$request->getParameter('page'));
return '';
// template name
templates/static/html/about.php

```



