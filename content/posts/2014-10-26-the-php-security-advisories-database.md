---
date: '2014-10-26'
title: The PHP Security Advisories Database
summary: |
    The PHP Security Advisories Database is now public domain and has moved to a new organization.
params:
    author: Fabien Potencier
url: /the-php-security-advisories-database.html
aliases:
    - /the-php-security-advisories-database
    - /article/74/the-php-security-advisories-database
---

A year and a half ago, I was very proud to [announce][1] a new initiative to
create a database of known security vulnerabilities for projects using
Composer. It has been a great success so far; many people extended the database
with their own advisories. As of today, we have vulnerabilities for Doctrine,
DomPdf, Laravel, SabreDav, Swiftmailer, Twig, Yii, Zend Framework, and of
course Symfony (we also have entries for some Symfony bundles like UserBundle,
RestBundle, and JsTranslationBundle.)

The security checker is now included by default in all new Symfony project via
`sensiolabs/SensioDistributionBundle`; checking vulnerabilities is as easy
as it can get:

    $ ./app/console security:check

If you are not using Symfony, you can easily use the [web interface][2], the
[command line tool][3], or the [HTTP API][4]. And of course, you are free to
build your own tool, based on the advisories stored in the "database".

Today, I've decided to get one step further and to clarify my intent with this
database: I don't want the database to be controlled by me or SensioLabs, I
want to help people find libraries they must upgrade now. That's the reason why
I've added a LICENSE for the database, which is now into the **[public domain][5]**.

Also, even if I've been managing this database since the beginning with only
good intentions, it is important that the data are not controlled by just one
person. We need one centralized repository for all PHP libraries, but a
distributed responsibility. As this repository is a good starting point, I've
decided to move the repository from the SensioLabs organization to the
**[FriendsOfPHP][6] organization**.

I hope that these changes will help the broader PHP community. So, who wants to
help?

[1]: http://fabien.potencier.org/article/67/don-t-use-php-libraries-with-known-security-issues
[2]: https://security.sensiolabs.org/check
[3]: https://github.com/sensiolabs/security-checker
[4]: https://security.sensiolabs.org/api
[5]: https://github.com/FriendsOfPHP/security-advisories/blob/master/LICENSE
[6]: https://github.com/FriendsOfPHP/security-advisories



