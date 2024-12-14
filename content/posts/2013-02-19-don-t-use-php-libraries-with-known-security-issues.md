---
date: '2013-02-19'
title: Don't use PHP libraries with known security issues
summary: |
    The one where I announce the launch of security.sensiolabs.org.
params:
    author: Fabien Potencier
url: /don-t-use-php-libraries-with-known-security-issues.html
aliases:
  - /don-t-use-php-libraries-with-known-security-issues.html
  - /article/67/don-t-use-php-libraries-with-known-security-issues
---

If you are a "connected" developer, you are probably aware of the major
vulnerabilities found in Ruby on Rails recently. To be fair, we've also found
some serious issues in the Symfony code during the last few months.

As security management should be a top most priority for us and our customers,
I've recently worked on improving the current situation in the Symfony world, with an
[enhanced security process](http://symfony.com/blog/security-issue-management-improvements).
But security management is also a very important topic for me because Symfony is
quickly growing in popularity for both end-user projects and Open-Source ones; and more
exposure also means more interest from the "bad guys".

One of the goal of good security issues management is **transparency**. That's
why the Symfony project has a simple way of reporting security issues (via the
security [at] symfony.com email address), an easily accessible list of
[security advisories](http://symfony.com/security#security-advisories), and
a well defined blog post template to announce security issues. Recently, we
have also enforced the need to have a [CVE identifier](http://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=symfony)
for all security issues to help the broader community to be aware of Symfony security issues.

The best advice one can give you is to upgrade your favorite libraries as soon
as possible when new versions with security fixes are released. Easier say
than done.

How do you know that a new release is out? How do you know that it contains
security fixes? For Symfony, you can subscribe to the RSS feed of the Symfony
[blog](http://feeds.feedburner.com/symfony/blog), or you can have a look at
our security advisories from time to time. But sometimes, that's not possible
as the project does not even have a blog. Was the security fix announced on
Twitter? Quite possible. But most of the time, smaller libraries just release
a new version without any proper security announcement.

But I want to provide a simple and efficient way to check for vulnerabilities
in a project and I want to serve more than just the Symfony community. That's
why I'm really proud to announce a new [SensioLabs](http://sensiolabs.com/)
initiative: a simple way to check if your project depends on third-party
libraries with known security issues. The website explains how it works in
details
([https://security.sensiolabs.org/](https://security.sensiolabs.org/)), but
basically, this initiative gives you several ways to check for security issues
in your project dependencies based on the information contained in you
`composer.lock` file (you are using Composer to manage your dependencies,
right?):

 * The **website** itself allows you to upload a `composer.lock` to check for
   vulnerabilities;

 * A **web service** can used with curl or to integrate that tool into your
   own continuous integration process (it returns its results as plain text or
   as a JSON array);

 * A **command line tool** gives you the same feature as the web service and
   the website but nicely packaged as a simple Symfony command.

Of course, the most important part of this initiative is the database where
known security vulnerabilities are referenced. The database is hosted on
Github: [https://github.com/sensiolabs/security-advisories](https://github.com/sensiolabs/security-advisories).
We have already referenced known vulnerabilities for Symfony, Zend Framework,
and some well-known Symfony bundles, but the idea is for the community to
help us add more libraries and more importantly to update the database whenever a security issue is fixed in a library.
Even if you don't have a way to easily announce your security fix to the world, at least,
reference it in the database; contributing to the database is really easy: fork
the repository, contribute your changes, and send a pull request (you can even do everything from the Github web interface if you want).

[Check](https://security.sensiolabs.org/check) your projects, upgrade your
dependencies when needed, and
[contribute](https://github.com/sensiolabs/security-advisories) to the
database!



