---
date: '2017-11-21'
title: 'Symfony 4: An Update on Flex'
summary: |
    A quick update on recent changes in Symfony Flex.
params:
    author: Fabien Potencier
url: /symfony4-flex-update.html
---

Symfony 4 is just around the corner. And Symfony Flex is one of the main selling points for the upgrade. Developers love the new philosophy. And a lot of changes happened since my last blog post. Let me recap the recent changes that you might not be aware of. Most of these changes were prompted by feedback from early adopters.

Using recipes from the [contrib repository](https://github.com/symfony/recipes-contrib) has became easier. The first time you require a package for which a contrib recipe exists, Flex now asks you the permission to execute the recipe. It also lets you switch the "accept contrib recipes" flag for the project (so it does not ask the question again). Much better experience, and better discoverability.

One issue people have had a lot is recipes installed multiple-times. I thought Flex got you covered except under some rare circumstances, but apparently those rare conditions are a bit more frequent than I anticipated. So, instead of trying to make clever hooks into the Composer life-cycle to determine if a recipe should be applied or not, each project now has a `symfony.lock` file where Flex's state is stored (this file must be committed in the project's code repository). Problem solved. And that lock file opens up some nice opportunities... but that's for another iteration.

Makefile support proves to be a nightmare. Despite several patches to the recipes having some Makefile support (support for Windows and whatnot), it was a never ending battle. At the end of the day, we realize that people had wrong expectations about the Makefile, and that `make` is not so standard. Instead of fighting the tool, we decided to add `symfony/console` as a requirement. Done. Simple. Efficient. And yes, Makefile support was introduced just as a facade to the optional Symfony Console component. You can still use a Makefile for your project of course. And I still think that this is a good idea.

The minimum version supported by Flex is now PHP 7.0 instead of PHP 7.1 previously to let more people use Flex. It's especially important for projects using Symfony 3.4, which is a long term support release. That will make the upgrade path to Symfony 4 smoother as well.

Having beta testers helped find bugs you would never have expected. When upgrading Flex, people had weird issues. That was because of how Composer plugins work. Some Flex classes are loaded early on by Composer. And if down the road, Flex is updated, you can have a situtation where a class from the newest version is mixed with classes from the old version. Not a good situation to be in. Now, Flex loads all its classes preemptively to be sure it is always in a consistent state.

In terms of features, Flex is now able to auto-register almost any bundle for which there are no recipes. And we added support for environment variables in PHPUnit configuration. Using Flex is also much faster as there are less roundtrips with the server (only one request per Composer session most of the time).

Regarding documentation, we have also started to document the new workflow with updates to the [upgrade tutorial](https://symfony.com/doc/4.0/setup/flex.html) and the [best practices document](https://symfony.com/doc/4.0/best_practices/creating-the-project.html). They now take into account changes introduced by Flex. However, keep in mind that documentation updates are still on-going.

Last, but not least, the Symfony Flex Server has a new UI, which allows you to better discover available recipes, aliases, and packs.

Happy Flex!
