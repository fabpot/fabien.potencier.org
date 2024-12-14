---
date: '2017-11-23'
title: Symfony Flex Private Repositories
summary: |
    Private recipes repositories support added to the Symfony Flex server.
params:
    author: Fabien Potencier
url: /symfony4-flex-private-repositories.html
---

**Private Repositories are not available anymore by lack of interest. This blog post is kept for history but it is obsolete.**

Many Flex early adopters asked for it. The Symfony Flex server now supports private recipes repositories as announced during my keynote at SymfonyCon Cluj.

Creating a repository for your private recipes is easy. Create a regular Github repository (probably a private one) to store the recipes. The directory structure is the same as for the [official Flex recipes repositories](https://github.com/symfony/recipes). Then, ~~register the repository as a recipes repository~~ (not available anymore). Done.

After registration, the repository will behave in the exact same way as the official ones, with the same features like automatic validation of pull requests by the Flex bot, and a dedicated endpoint to test a pull request on a project.

As for the main recipes repository, but unlike the contrib one, private repositories can define aliases and override existing ones. Your aliases take precedence over the official ones. If you don't like our default choice for the `admin` or `orm` aliases, override them. You can also override a recipe to customize the package's default configuration.

There are some differences with the public repositories though: of course, you can define recipes for your private packages. Auto-merge of pull requests is not supported yet. And for Makefile lovers, I have some good news: private repositories support Makefile (as you have full control of your stack).

How do you configure the projects that can have access to your private repository? In a `config.json` file located at the root directory of the repository. Under the `projects` key, list the project's ULID (as found in `composer.json` or via `composer config extra.symfony.id`):

```json
{
    "projects": {
        "01BWTQ6H4KJFHRZ240CXBN6S6A": "my super secret project",
        "01BYV69F2S7ECNN3N76K82BV4W": "demo"
    }
}
```

While in beta, private repositories are free.

Happy private Flex!
