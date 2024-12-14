---
date: '2009-02-03'
title: Getting information from SVN with PHP 
summary: |
    Last year, I deployed a new tool to manage symfony plugins.
    The first goal of this tool was to simplify the process of contributing new plugins. It proved to be very successful,
    and I had a good feeling that the plugin creation rate was rising since then. And yesterday, I wanted to have
    hard numbers to back me up.
params:
    author: Fabien Potencier
url: /getting-information-from-svn-with-php.html
aliases:
  - /article/7/getting-information-from-svn-with-php
  - /getting-information-from-svn-with-php
---

Last year, I deployed a new tool to manage [symfony plugins](http://www.symfony-project.org/plugins/).
The first goal of this tool was to simplify the process of contributing new plugins. It proved to be very successful,
and I had a good feeling that the plugin creation rate was rising since then. And yesterday, I wanted to have
hard numbers to back me up.

The question I wanted to answer was quite simple:

*How many plugins were created per month before and after the change?*

As the symfony project provides [free SVN hosting](http://svn.symfony-project.com/plugins/) for
community plugins, most plugins are actually hosted
on the official symfony Subversion repository. And thankfully, the `svn` command line can give you a lot
of information about your project in a very friendly format to parse: XML.

The first step is to get the log file as an XML file:

    $ svn log --xml --verbose http://svn.symfony-project.com/plugins > svn_log.xml

The output is similar to something like this:


```xml
<?xml version="1.0"?>
<log>
  <logentry revision="1949">
    <author>fabien</author>
    <date>2006-09-05T14:40:20.603942Z</date>
    <paths>
      <path action="A">/plugins/sfGuardPlugin</path>
    </paths>
    <msg>added sfGuardPlugin</msg>
  </logentry>

  <!-- many more entries -->
</log>

```

This XML file contains everything I need: the date of each commit and the
paths that were modified, added, or deleted.

Reading an XML file with PHP is insanely easy thanks to `simplexml`:


```php
$xml = simplexml_load_file(dirname(__FILE__).'/svn_log.xml');

```

The `simplexml_load_file()` function reads an XML file and returns an object
that can manipulated to access the various information contained in the
XML.

Iterating over the `logentry` and extracting data is also simple enough:


```php
foreach ($xml->logentry as $logentry)
{
  $date = (string) $logentry->date;

  foreach ($logentry->paths as $paths)
  {
    foreach ($paths->path as $path)
    {
      $action = $path['action'];
      $content = (string) $path;
    }
  }
}

```

Each tag is accessible as a property of the object and returns an object
representing the XML child elements:


```php
$xml->logentry;

```

As SimpleXML returns objects, we sometimes need to cast them to string:


```php
$date = (string) $logentry->date;

```

Getting a tag attribute is as simple as using the object as an array:


```php
$action = $path['action'];

```

Eventually, getting the content of a tag can be done by casting the object
itself to a string:


```php
$content = (string) $path;

```

Without further ado, the following code parses the XML file and detect plugin
creation by checking if the path has been created (`A`) and if the path is of
the form `/plugins/XXXPlugin` (a symfony plugin name always ends with `Plugin`).


```php
$numbers = array();

$xml = simplexml_load_file(dirname(__FILE__).'/svn_log.xml');
foreach ($xml->logentry as $logentry)
{
  $date = date('d/m/Y', strtotime((string) $logentry->date));
  foreach ($logentry->paths as $paths)
  {
    foreach ($paths->path as $path)
    {
      if ('A' == $path['action'] && preg_match('#^/plugins/.+Plugin$#', (string) $path, $matches))
      {
        if (!isset($numbers[$date]))
        {
          $numbers[$date] = 1;
        }
        else
        {
          ++$numbers[$date];
        }
      }
    }
  }
}

```

The `$numbers` array contains the days as keys, and the number of plugins created
for this specific particular day as values.

To create a graph, the array can be exported as a simple CSV file to be read
by Excel:


```php
foreach ($numbers as $date => $number)
{
  echo "$date,$number\r\n";
}

```

It outputs the following data:

    02/02/2009,1
    01/02/2009,1
    31/01/2009,1
    30/01/2009,2
    29/01/2009,1
    27/01/2009,3
    ...

And here is the graph for the data as of today:

![Plugin Creation Rate](http://www.symfony-project.org/uploads/assets/plugin_creation_rate.png)

I was right. We more than doubled the number of plugin creation per month
after the change: from 14 plugins to 30 plugins per month.



