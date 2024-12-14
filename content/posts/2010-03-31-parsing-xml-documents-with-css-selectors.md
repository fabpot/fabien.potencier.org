---
date: '2010-03-31'
title: Parsing XML documents with CSS selectors
summary: |
    The one where I talk about the new and shiny Symfony 2 CSS Selector Component, a tool that converts CSS selectors to XPath expressions.
params:
    author: Fabien Potencier
url: /parsing-xml-documents-with-css-selectors.html
aliases:
  - /parsing-xml-documents-with-css-selectors
  - /article/42/parsing-xml-documents-with-css-selectors
---

HTML and XML documents are the bread and butter of web developers. On a day to
day basis, you probably create a lot of HTML documents. And odds are you also
need to parse some from time to time: because you consume a web service and
want to extract some information, or because you want to gather data from
scraped web pages, or just because you want to write functional tests for a
website. Retrieving the document is quite easy, but how do you navigate
through it to extract the information you need?

PHP already comes with a lot of useful tools for parsing XML documents:
`SimpleXML`, `DOM`, and `XMLReader`, just to name a few. But as soon as you
need to extract information deeply embedded in the document structure, things
are not as easy as they should be. Of course, XPath is your best friend when
you need to select elements, but the learning curve is really steep. Even
expressions that should be easy can be complex. As an example, here is the
XPath expression to retrieve all `h1` tags that have a `foo` class:

    h1[contains(concat(' ', normalize-space(@class), ' '), ' foo ')]

The XPath expression is complex because a tag can have several classes:


```html
<h1 class="foo">Foo</h1>
<h1 class="foo bar">Foo</h1>
<h1 class="foobar bar">Foo</h1>

```

The expression should match the first two `h1` tags, but not the third one.

Of course, everybody knows that doing the same with a CSS selector is a piece
of cake:

    h1.foo

For Symfony 2 functional tests, I wanted a way to leverage the power and
expressiveness of CSS selectors with the tools we already have in PHP. The
first idea that came to my mind was to convert a CSS selector to its XPath
equivalent. But is it possible? The answer is a surrounding 'YES'.

As John Resig wrote in a blog
[post](http://ejohn.org/blog/xpath-css-selectors/) some time ago about the
same topic: "The biggest thing to realize is that CSS Selectors are,
typically, very short - but woefully underpowered, when compared to XPath."

Writing a tokenizer, a parser, and a compiler able to convert CSS selectors to
XPath is no trivial task. So, instead of reinventing the wheel, I looked for
some existing libraries. I didn't look too much before stumbling upon `lxml`, a
Python library. The
[`lxml.cssselect`](http://codespeak.net/lxml/cssselect.html) module of `lxml`
does exactly this. So, I took the time to translate the Python code to PHP,
added some unit tests, and voilà, the [Symfony 2 CSS Selector component](http://github.com/fabpot/symfony/tree/master/src/Symfony/Components/CssSelector/) was
born.

{{< notice note >}}
symfony 1 has a `sfDomCssSelector` class, but it does not convert the CSS
selector to XPath. It does the job nicely but it is limited to very simple
CSS selectors and it cannot easily be used with standard XML tools.
{{< /notice >}}

The Symfony 2 CSS Selector component does only one thing, and it tries to do
it well: *converting CSS selectors to XPath expressions*. Using it is dead
simple:


```php
use Symfony\Components\CssSelector\Parser;

$xpath = Parser::cssToXpath('h1.foo');

```

The `$xpath` variable should now contain `h1[contains(concat(' ',
normalize-space(@class), ' '), ' foo ')]`.

Let's take an example to see how you can use it. Let's say you want to
retrieve all post titles and URLs for this blog (the information is available
at `http://fabien.potencier.org/articles`).


```php
use Symfony\Components\CssSelector\Parser;

$document = new \DOMDocument();
$document->loadHTMLFile('http://fabien.potencier.org/articles');

$xpath = new \DOMXPath($document);
foreach ($xpath->query(Parser::cssToXpath('div.item > h4 > a')) as $node)
{
  printf("%s (%s)\n", $node->nodeValue, $node->getAttribute('href'));
}

```

The code is straightforward and instead of using an XPath expression, we let
the Parser class convert the CSS Selector to XPath for us:


```php
$xpath->query(Parser::cssToXpath('div.item > h4 > a'))

```

Be warned that if you work with XML documents, you need to register the
namespaces you use. Let's use `SimpleXMLElement`, which only understand
well-formed XML documents:


```php
$document = new \SimpleXMLElement('http://fabien.potencier.org/articles', 0, true);
$document->registerXPathNamespace('xhtml', 'http://www.w3.org/1999/xhtml');
foreach ($document->xpath(Parser::cssToXpath('xhtml|div.item > xhtml|h4 > xhtml|a')) as $node)
{
  printf("%s (%s)\n", $node, $node['href']);
}

```

As you can notice, CSS selectors support namespaces (`xhtml|div`).

This new CSS Selector component will be used in Symfony 2 for functional
tests (but as you will see in the coming weeks, in a very different way than
what we had in symfony 1).

The code is unit-tested and has a good code coverage, so feel free to
[use](http://github.com/fabpot/symfony/tree/master/src/Symfony/Components/CssSelector/)
it (code is on Github: `http://github.com/fabpot/symfony` under the
`Symfony\Components\CssSelector` namespace) and send me some feedback.

Stay tuned!
