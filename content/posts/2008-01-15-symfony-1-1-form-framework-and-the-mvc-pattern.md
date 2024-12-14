---
date: '2008-01-15'
title: 'symfony 1.1 form framework and the MVC pattern'
summary: |
    In this post, I try to explain the philosophy behind the new symfony form framework and how it sticks to the MVC pattern.
params:
    author: Fabien Potencier
url: /symfony-1-1-form-framework-and-the-mvc-pattern.html
aliases:
  - /symfony-1-1-form-framework-and-the-mvc-pattern
  - /article/3/symfony-1-1-form-framework-and-the-mvc-pattern
---

Even if the symfony 1.1 form framework is not yet documented, a lot of people are already using it. That's great news as I think this is one of the major new features of symfony 1.1 and probably the one I'm the most proud of.

Recently on the [symfony user mailing-list](http://groups.google.com/group/symfony-users), someone asked some very interesting [questions about this new form framework](http://groups.google.com/group/symfony-users/browse_thread/thread/1f6a18592d75ad82) and how it fits with the [MVC pattern](http://en.wikipedia.org/wiki/Model-view-controller).

I've spent more than a year and a half designing and coding the form framework and most of this time was spent ensuring it sticks to the MVC pattern. So, I was a bit disappointed when I read his questions. I will try to explain the MVC philosophy behind the new form framework in this post.

Before I start, you may have noticed that I call the new form system a "framework". That's right, it is a framework by itself. You can use it without symfony. There is no dependency between the new form framework and the rest of symfony. As a matter of fact, hartym has packaged the form framework in a [plugin](http://www.symforc.com/post/2008/01/07/sfForms11Plugin%3A-use-symfony-11-form/validation-framework-in-symfony-10-or-non-symfony-project) for symfony 1.0. This also means that you can use it with the Zend Framework or even with plain PHP. The only requirements is to use PHP 5.1.3 or later.

What's a symfony form?
======================

A symfony form (`sfForm`) is an object able to validate input data and to be rendered as HTML.

But the form itself does nothing more than just delegating those two tasks to embedded objects: a validator schema (`sfValidatorSchema`) and a widget schema (`sfWidgetSchema`):


```php
class DemoForm extends sfForm
{
  function configure()
  {
    $this->setValidatorSchema(new sfValidatorSchema(/* validators configuration */));
    $this->setWidgetSchema(new sfWidgetFormSchema(/* widgets configuration */));
  }
}

```

In your action, here is how to use the `DemoForm`:


```php
function executeIndex($request)
{
  $this->form = new DemoForm(array(/* initial values */));

  if ($request->isMethod('post'))
  {
    $this->form->bind($request->getParameter('demo'));
    if ($this->form->isValid())
    {
      // do something with cleaned values
      $values = $this->form->getValues();

      $this->redirect('@somewhere');
    }
  }
}

```

So, how does the form framework stick to the MVC pattern?
=====================================================

Before reinventing the wheel, I've read a lot of code and documentation: mainly libraries in PHP, Perl, and Python.

The symfony form framework is greatly inspired by two great Python libraries: [FormEncode](http://formencode.org/) and [Django newforms](http://www.djangoproject.com/documentation/newforms/).

Those two libraries have done a wonderful work but I think the symfony form framework does one thing better: it is really MVC compliant. I have put the extra effort to ensure that every single line of code belongs to the right layer and that all layers are really decoupled and useable by themselves.

Here is an excerpt of the [Wikipedia MVC pattern definition](http://en.wikipedia.org/wiki/Model-view-controller):

{{< notice quote >}}
{{< /notice >}}

And here is my annotated description of the pattern from Wikipedia:

The Model
---------

{{< notice quote >}}
{{< /notice >}}

The **validator classes** handle the logic needed to validate the raw data and clean/convert the data to validated and consistent data (convert dates to timestamp, convert the unusable PHP `$_FILES` array to `sfValidatedFile` objects, etc...). Moreover, the validator sub-framework is totally decoupled from the rest of symfony. So, you can use it by itself. You can use it to validate any input source, be it an XML file, objects...

The View
--------

{{< notice quote >}}
{{< /notice >}}

The **widgets classes** render the form (in HTML by default) to display it to the user. Like the validator sub-framework, the widget sub-framework is totally decoupled from the rest of symfony... which means you can use it by itself. In fact, we will use it for non form widgets in the future.

The Controller
--------------

{{< notice quote >}}
{{< /notice >}}

The **form classes** create the form, respond to the user `POST` or `GET` requests, invoke the form validation (`->bind()` `->isValid()`) and trigger the model (whatever you do with the validated data).

{{< notice note >}}
{{< /notice >}}

The Action
==========

Let's dive in the action example we've seen at the beginning.

It creates a form and passes it to the view (thanks to the `$this->` convention):


```php
$this->form = new DemoForm(array(/* initial values */));

```

The form is then bound to the request data and the binding also triggers the validation. The validation itself is managed by the validator schema, not the form.


```php
$this->form->bind($request->getParameter('demo'));

```

If the form is valid, it's then up to the developer to do something useful with the validated data:


```php
$values = $this->form->getValues();

```

The action also controls the flow of the request. In the example, the validation is only triggered on `POST` request:


```php
if ($request->isMethod('post'))
{
}

```

And in case of a valid form, it redirects the request:


```php
$this->redirect('@somewhere');

```

{{< notice note >}}
{{< /notice >}}

The Template
============

The form is rendered in the template. The simplest way to render the form is:


```php
<?php echo $form ?>

```

But, keep in mind that the `echo $form` is just a nice shortcut. It's a great way to prototype a working form but most of the time, you want greater control on the form layout and how to arrange the different widgets together. And this is where the new system really shines. The designers work is greatly simplified. Let's compare how to display an input tag in symfony 1.0 and symfony 1.1:


```php
// symfony 1.0
<?php echo input_tag('author[first_name]', $sf_params->get('author[first_name]', 'initial data'), array('class' => 'foo')) ?>

// symfony 1.0 with the fill-in filter enabled
<?php echo input_tag('author[first_name]', 'initial data', array('class' => 'foo')) ?>

// symfony 1.1 (fill-in and initial data just works)
<?php echo $form['author']['first_name'] ?>

// symfony 1.1 if the class has not been configured in the widget schema
<?php echo $form['author']['first_name']->render(array('class' => 'foo')) ?>

```

I really think designers will love the new system.

Here are some other snippets to demonstrate the flexibility:


```php
// render errors for a field as a list
<?php echo $form['author']['first_name']->renderError() ?>

// render the label tag for a field (with the proper for attribute)
<?php echo $form['author']['first_name']->renderLabel() ?>

// render the whole row for a field (label, tag, help and errors)
<?php echo $form['author']['first_name']->renderRow() ?>

```

Wrap-up
=======

I hope I've clarified the philosophy of the new form framework a bit and how it really sticks to the MVC pattern.

One last note about the new form framework. As a form is a first-class citizen, you have all the advantages of object oriented systems:

  * You can extend an existing form
  * You can customize validators and widgets
  * You can package a whole form (with validators and widgets) and share it
  * Everything is centralized in one place and not scattered in multiple directories



