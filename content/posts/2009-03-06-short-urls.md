---
date: '2009-03-06'
title: Short URLs
summary: |
    Websites like Twitter makes URL shortener services a must. My main concern with URL shortening is that you loose all the meaning embedded in the URLs. I wanted a shortener URL service that gives short URLs, but also keep some
    sort of meaningful information about the original URL.
params:
    author: Fabien Potencier
url: /short-urls.html
aliases:
  - /article/10/short-urls
  - /short-urls
---

Websites like Twitter makes URL shortener services a must. Basically, they
convert long URLs to shorter ones by assigning a mostly unique hash:

The `tinyurl.com` service for example converts this URL `http://www.symfony-project.org/blog/2009/02/18/dailymotion-powered-by-symfony` to this shorter one `http://tinyurl.com/css7s5`.

My main concern with URL shortening is that you loose all the meaning embedded
in the URLs. What does `css7s5` refers to? Nothing. It is just a hash of the
original URL. It is also very difficult to remember a short URL.

I wanted a shortener URL service that gives short URLs, but also keep some
sort of meaningful information about the original URL. The solution was to
implement my own little URL shortener service, specialized for my needs.

The first thing I did was to register a short domain name: `sf-to.org` (sf
meaning symfony here).

Then, I simply configured rewriting rules for the short URLs I wanted to have:

    <VirtualHost *:80>
      ServerName sf-to.org

      RewriteEngine On
      RedirectMatch permanent /bookmarks   http://www.symfony-project.org/blog/2006/10/28/yahoo-bookmarks-uses-symfony
      RedirectMatch permanent /answers     http://www.symfony-project.org/blog/2008/05/08/yahoo-answers-powered-by-symfony
      RedirectMatch permanent /delicious   http://www.symfony-project.org/blog/2007/10/02/delicious-preview-built-with-symfony
      RedirectMatch permanent /dailymotion http://www.symfony-project.org/blog/2009/02/18/dailymotion-powered-by-symfony

      # ...
    </VirtualHost>

The short URL for the above example is now: `http://sf-to.org/dailymotion`.

Of course, if I start needing more short URLs, I will probably write a small
PHP script. But for now, it gets the job done.



