---
date: '2018-01-04'
title: 'Symfony 4: Unpack the Packs'
summary: |
    Introducing a way to unpack the packs.
params:
    author: Fabien Potencier
url: /symfony4-unpack-the-packs.html
---

We get a lot of positive feedback on Symfony 4. You love the new directory structure. You love the simplicity of using services without configuring anything. And you love the automation that Flex provides. It makes me happy and proud.

But one issue I hear sometimes is that it is more complex to start a new project. Most projects need many Symfony features. But new projects now have bare minimum dependencies when created via `composer create-project symfony/skeleton`. You need to explicitly add all the dependencies you want to depend on. Being explicit is great, but UX suffers. Ever tried to add ``profiler`` and didn't get timing info? Yup, ``symfony/stopwatch`` is optional, so you need to add it explicitly.

You loved the simplicity of starting a project with the Symfony Standard Edition. Is it nostalgia? Perhaps. Would it be possible to get the best of both world? Certainly! Read on.

First, let me recap some current available features:

 * Any Composer package can have a related recipe that helps with
   auto-configuration (recipes are stored at `symfony/recipes` and
   `symfony/recipes-contrib`);

 * Symfony Packs are Composer metapackages that bundle several dependencies
   together to make your life easier (install `symfony/debug-pack` and get
   everything you need to debug a Symfony application).

Symfony Packs are time savers, but they have one limitation: they mask the real dependencies. Let me explain with an example. Using `symfony/orm-pack` is a great way to get the most commonly needed Doctrine related packages and bundles. Run `composer req symfony/orm-pack` (or simply `composer req orm`) to get Doctrine core, the Doctrine bundle, and the Doctrine migrations bundle (everything configured thanks to some nice recipes).

But what if you want to remove one dependency that is included in the pack? Like the migrations bundle. You cannot remove it as the project's `composer.json` file requires `symfony/orm-pack`, not the individual dependencies:

```diff
diff --git a/composer.json b/composer.json
index e24f26b..5238f98 100644
--- a/composer.json
+++ b/composer.json
@@ -7,6 +7,7 @@
         "symfony/flex": "^1.0",
         "symfony/framework-bundle": "^4.0",
         "symfony/lts": "^4@dev",
+        "symfony/orm-pack": "^1.0",
         "symfony/yaml": "^4.0"
     },
     "require-dev": {
```

Another example would be when you want to change a dependency constraint for a package coming from a pack.

You can of course require the individual dependencies, but for packs like `debug` or `api`, you would have to have a look at their `composer.json` on Github and do some copy/paste. Not ideal.

There is another way. Unpacking the pack. You can now `unpack` an already installed pack via the `unpack` command:

```bash
composer unpack orm
```

The command updates `composer.json` to remove the pack and replace it with the individual dependencies defined in the pack:

```diff
diff --git a/composer.json b/composer.json
index 5238f98..b8c9794 100644
--- a/composer.json
+++ b/composer.json
@@ -3,11 +3,13 @@
     "license": "proprietary",
     "require": {
         "php": "^7.1.3",
+        "doctrine/doctrine-bundle": "^1.6.10",
+        "doctrine/doctrine-migrations-bundle": "^1.3",
+        "doctrine/orm": "^2.5.11",
         "symfony/console": "^4.0",
         "symfony/flex": "^1.0",
         "symfony/framework-bundle": "^4.0",
         "symfony/lts": "^4@dev",
-        "symfony/orm-pack": "^1.0",
         "symfony/yaml": "^4.0"
     },
     "require-dev": {
```

Tweaking the pack dependencies is now possible. Don't want the migration bundles? Simple enough:

```bash
composer rem migrations
```

You can also unpack a pack at installation time via the `--unpack` flag. That flag tells Composer to add the dependencies of the pack in your `composer.json` instead of adding the pack package itself:

```bash
composer req orm-pack --unpack
```

Note that the `unpack` command and the `--unpack` flag only work for Symfony packs (the Composer package type must be `symfony-pack`). Any other dependencies are simply ignored and follow the standard Composer installation process.

That's a great feature by itself and give you even more power when it comes to dependency management.

Now, you could create a "Symfony Standard Edition" pack and benefit from the new unpacking feature.

That would almost work. Except that the Symfony Standard Edition has dev dependencies that would not be installed by Composer. As you know, when adding a dependency, Composer will install any transitive dependencies from the `require` section, but it does not care about the ones defined under the `require-dev` section.

To get to the next level, and because you can now unpack a pack very easily, a Standard Edition can be re-created simply by having a new skeleton.

To demonstrate how that works, let's take the brand new ["Symfony Website Skeleton"](https://github.com/symfony/website-skeleton/blob/4.0/composer.json) as an example.

This new skeleton *almost* replicates Symfony Standard Edition's dependencies:

```json
{
    "name": "symfony/website-skeleton",
    "type": "project",
    "license": "MIT",
    "description": "A skeleton to start a new Symfony website",
    "require": {
        "php": "^7.1.3",
        "ext-iconv": "*",
        "sensio/framework-extra-bundle": "^5.1",
        "symfony/asset": "^4.0",
        "symfony/browser-kit": "^4.0",
        "symfony/console": "^4.0",
        "symfony/css-selector": "^4.0",
        "symfony/debug-pack": "*",
        "symfony/expression-language": "^4.0",
        "symfony/flex": "^1.0",
        "symfony/framework-bundle": "^4.0",
        "symfony/form": "^4.0",
        "symfony/lts": "^4@dev",
        "symfony/orm-pack": "*",
        "symfony/monolog-bundle": "^3.1",
        "symfony/process": "^4.0",
        "symfony/security-bundle": "^4.0",
        "symfony/serializer-pack": "*",
        "symfony/validator": "^4.0",
        "symfony/swiftmailer-bundle": "^3.1",
        "symfony/web-link": "^4.0",
        "symfony/webpack-encore-pack": "*",
        "symfony/yaml": "^4.0"
    },
    "require-dev": {
        "symfony/dotenv": "^4.0",
        "symfony/maker-bundle": "^1.0",
        "symfony/phpunit-bridge": "^4.0",
        "symfony/profiler-pack": "*"
},
...
```

Run `composer create-project symfony/website-skeleton` to start a new project. You get everything you need to get started fast. Use the `unpack` command if you need greater control over the added dependencies; unpack, remove, add as you see fit. You have full control. Note that if you unpack a dependency defined under `require-dev`, all its dependencies are added to the `require-dev` section as expected.

Like Silex ? Start with `symfony/skeleton`. Like the full-stack Symfony framework better? Start with `symfony/website-skeleton`. Then, be free to add or remove dependencies. Scale the way *you* want your project.

Packs makes it easy to create great skeletons. And unpack allows to keep the flexibility of being explicit and precise about the dependencies.

The new unpack feature opens a lot of possibilities and gives you even more flexibility and control on your project's dependency management. Mind blowing if you ask me.

Happy Symfony!
