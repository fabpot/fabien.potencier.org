---
date: '2009-11-28'
title: Pirum, the Simple PEAR Channel Server Manager
summary: |
    Some weeks ago during the Zend Conference, I
    quietly released
    Pirum, a simple PEAR channel server manager.
    As some people talk about it on "social
    networks", I thought I
    should write an official announcement on my blog to explain where I come from.
params:
    author: Fabien Potencier
url: /pirum-the-simple-pear-channel-server-manager.html
aliases:
    - /pirum-the-simple-pear-channel-server-manager
    - /article/38/pirum-the-simple-pear-channel-server-manager
---

Some weeks ago during the Zend Conference, I
[quietly](http://twitter.com/fabpot/status/4969508481) released
[Pirum](http://www.pirum-project.org/), a simple PEAR channel server manager.
As some people talk about it on "social
[networks](http://twitter.com/s_bergmann/statuses/6121431277)", I thought I
should write an official announcement on my blog to explain where I come from.

A PEAR channel server allows you to install PEAR packages with the PEAR
command line. You are probably already familiar with it as it comes bundled
with most PHP installations:

    $ pear install ...

Pirum comes from my frustration with the current state of PEAR channel
servers. Beside big Open-Source projects like
[symfony](http://www.symfony-project.org/), I'm also responsible for smaller
projects (like [Twig](http://www.twig-project.org/), or [Swift Mailer](http://www.swiftmailer.org/)).
But for all my software, I like to provide different way
of installing them: directly from SVN via `svn:externals`,
from my [Git](http://www.github.com/fabpot) mirrors, from an
archive to download, or with the PEAR command line.

Except for the PEAR package installation, the other ones are quite easy to
setup and automate. But providing a PEAR channel proves to be more involving.
The only serious PEAR channel server I'm aware of is
[Chiara_PEAR_Channel](http://greg.chiaraquartet.net/archives/123-Setting-up-your-own-PEAR-channel-with-Chiara_PEAR_Server-the-official-way.html)
from Greg Beaver. This is the software I used for symfony for four years. It
works really well, but is a bit cumbersome to setup for smaller projects (it
needs a database, and you need to configure some categories, the contributors
for the project, and more). That's fine for [PEAR](http://pear.php.net)
itself, but frankly, for the rest of us, that's just too much.

As a matter of fact, hosting a PEAR channel server is super simple. It's all
about static files. That's right, even if you need some sort of PHP scripts to
maintain a PEAR channel server, the frontend used by the PEAR command line
tool can only be static files (mainly XML ones). And `Chiara_PEAR_Server`
works exactly like this. The backend, done in PHP, is where you maintain your
packages, and it generates the frontend, a bunch of static files for the
frontend. That's a really great architecture as it allows to scale very
easily. For instance, you should probably be able to host a PEAR channel
server on a CDN line Amazon S3 in a matter of minutes.

Thanks to this decoupled and simple architecture, I wrote some PHP scripts to
manage a PEAR channel server for the symfony
[plugins](http://www.symfony-project.org/plugins/) two years ago.

The idea for Pirum came when I started the
[Twig](http://www.twig-project.org/) project. Obviously, I wanted a PEAR
channel server for Twig, but I really did not want to use
`Chiara_PEAR_Server`. So I started to hack my own and Pirum was born.

Pirum lets you setup PEAR channel servers in a matter of minutes. Pirum is
best suited when you want to create small PEAR channels for a few packages
written by a few developers.

Pirum consists of just one file, a command line tool, written in PHP. There is
no external dependencies, no not need for a database, no need to setup
credentials, and nothing need to be installed or configured.

Installing Pirum is as simple as downloading the
[pirum](http://github.com/fabpot/Pirum/raw/master/pirum) file and saving it
where you see fit.

{{< notice note >}}
Of course, Pirum itself uses Pirum to provide a PEAR channel
[server](http://pear.pirum-project.org/) for itself!
{{< /notice >}}

Even if Pirum only consists of just one file, it comes with a lot of great
features:

Besides its size and simplicity, Pirum is packed with a lot of features:

 * It creates a full-featured PEAR channel server useable by any PEAR CLI;

 * Each channel has an HTML page describing the server and the packages it
   hosts;

 * New releases can be tracked by subscribing to an Atom feed.

Of course, it also comes with some limitations:

 * No support for fallback PEAR channel servers;

 * No category management (all packages are under a "default" category);

 * No web interface for managing the packages.

You can find more information about using Pirum on its official
[website](http://www.pirum-project.org/).

Pirum is already used by the following Open-Source projects:

   * [Symfony](http://www.symfony-project.org/)

   * [PHPUnit](http://pear.phpunit.de/)

   * [Swift Mailer](http://www.swiftmailer.org/)

   * [Twig](http://www.twig-project.org/)

   * [FluentDOM](http://www.fluentdom.org/)

   * [Pirum](http://www.pirum-project.org/)

I hope that with Pirum, more Open-Source projects will start providing a PEAR
channel as a mean to install their software. If you start using Pirum for your
project, please send me an email so that I can update the list on the Pirum
website.



