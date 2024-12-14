---
date: '2009-06-14'
title: Tweeting from PHP
summary: |
    Twitter is everywhere nowadays. Odds are eventually you will want to tweet
    from PHP. No need to use one of the numerous PHP Twitter libraries, as
    tweeting is as simple as using the PHP built-in `file_get_contents()`
    function.
params:
    author: Fabien Potencier
url: /tweeting-from-php.html
aliases:
  - /tweeting-from-php
  - /article/20/tweeting-from-php
---

Twitter is everywhere nowadays. Odds are eventually you will want to tweet
from PHP. No need to use one of the numerous PHP Twitter libraries, as
tweeting is as simple as using the PHP built-in `file_get_contents()`
function:


```php
function tweet($message, $username, $password)
{
  $context = stream_context_create(array(
    'http' => array(
      'method'  => 'POST',
      'header'  => sprintf("Authorization: Basic %s\r\n", base64_encode($username.':'.$password)).
                   "Content-type: application/x-www-form-urlencoded\r\n",
      'content' => http_build_query(array('status' => $message)),
      'timeout' => 5,
    ),
  ));
  $ret = file_get_contents('http://twitter.com/statuses/update.xml', false, $context);

  return false !== $ret;
}

```

Pretty easy, no? Using the `tweet()` function is of course a piece of cake:


```php
tweet('From PHP, yeah...', 'fabpot', 'Pa$$');

```

As an added bonus, the function returns `true` if everything went fine, or
`false` otherwise.

As we talk about Twitter, you can [follow me](http://www.twitter.com/fabpot).



