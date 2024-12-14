---
date: '2007-11-20'
title: 'A symfony tip: Upgrade a project running symfony 1.0'
summary: |
    Learn how to stay up to date with the symfony 1.0 branch.
params:
    author: Fabien Potencier
url: /a-symfony-tip-upgrade-a-project-running-symfony-1-0.html
aliases:
    - /a-symfony-tip-upgrade-a-project-running-symfony-1-0
    - /article/25/a-symfony-tip-upgrade-a-project-running-symfony-1-0
---

symfony 1.0 is the latest stable symfony release.

One of the main advantage of using it for your next project is the stability of this version
and because [Sensio](http://www.sensio.com/) is committed to maintain it for a long period of time.

You know that bugs will be fixed and that no new feature will break your code.

When creating a new project, you have to make a decison: How to install and embed symfony?

The first way of installing symfony is to use the PEAR package. This is the easiest installation method:

    $ pear channel-discover pear.symfony-project.com
    $ pear install symfony/symfony-stable

As symfony will be installed globally on your machine, every project will share the same version
of symfony. So, each time you upgrade symfony via PEAR, all your projects will be upgraded:

    $ pear upgrade symfony/symfony

...but then, if you host your project on a shared server, PEAR installation is not easy.

If you use Subversion for your projects, there is a better way.

Subversion is able to make a "link" from your project repository to an external Subversion repository.

So, if you want to embed symfony in your project using Subversion, here are some steps to follow:

First, let's create a `lib/vendor` directory and add it to the repository:

    $ mkdir lib/vendor
    $ svn add lib/vendor
    $ svn ci -m"added lib/vendor directory" lib/vendor

Now, let's create the external link to the symfony repository:

    $ svn propedit svn:externals lib/vendor

Your favorite text editor popups. Add this at the beginning of the file and save it:

    symfony http://svn.symfony-project.com/branches/1.0

Now, run a Subversion update:

    $ svn up

This way, as symfony is embedded in your project, deployment is very easy and if you want to upgrade symfony
to the latest 1.0 version, the only thing you have to do is to run the `svn update` command:

    $ svn up



