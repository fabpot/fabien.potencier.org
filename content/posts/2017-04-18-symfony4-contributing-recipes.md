---
date: '2017-04-18'
title: 'Symfony 4: Contributing Recipes'
summary: |
    The Symfony Recipes are opinionated, but anyone can contribute.
params:
    author: Fabien Potencier
url: /symfony4-contributing-recipes.html
---

Symfony Flex is not Open-Sourced yet, but I can already feel the excitement in
the community. I have also received some feedback via Twitter, Slack, and email
during the last few days. That makes me really happy!

Everybody knows that I like to tease. Trying to build the momentum on something
you have been working on for months feels great. But the reality is a bit more
complex: I am still working on the first MVP of the infrastructure needed to
support Symfony Flex. To be honest, I'm probably working on something that is a
bit more than just an MVP.

The good news is that I'm almost there. The bad news is that some feedback made
me realize that some features I had on my todo list for later need to be part
of the first version. You know, that "one more feature" that will make Symfony
Flex the next killer application for the Symfony community.

People are worried about the opinionated recipe repository. Keep in mind that
one of the main goals of Symfony Flex is to automate your day-to-day workflow
for the happy path.

First, I want to reiterate that a package does not need to have a recipe to be
installed. The good old way of registering bundles manually and copy/pasting
some default configuration will still work fine... which is what everybody has
been doing for years. In other words, everything will be at least as easy to
install as it is now.

Then, for several reasons I explained in my previous post, I won't change my
mind about the official repository being opinionated. I talk to a lot of
developers and companies using Symfony, and many want "us" to make choices for
them. They don't want to test dozens of bundles to make an informed choice.

Finally, I need to reveal one more recipe configuration option that I did not
mention yet: *aliases*. Instead of using the regular Composer package name, a
recipe can list alternative shorter names. For instance, use `composer req cli`
instead of `composer req symfony/console`. You can imagine packages willing to
reserve the `admin`, `api`, or `orm` aliases. And of course, each alias can
only be linked to one Composer package.

Choices are good.

But what about having another recipe repository along side the main one? Well,
that feature was on my todo list for a future version. But I have decided to
include it now. For the first version. And I like it a lot.

Let's dive into the Symfony Flex recipe repositories. Instead of having just
one repository, Symfony Flex supports two.

Here is how it works:

 * The "main" official repository, hosted at
   [https://github.com/symfony/recipes](https://github.com/symfony/recipes)
   will be opinionated. Submissions will be reviewed carefully. They will need
   approval from the Symfony core team (like pull requests on the main
   `symfony/symfony` repository). We want the utmost quality and the *best*
   developer experience.

 * The "contrib" repository, hosted at
   [https://github.com/symfony/recipes-contrib](https://github.com/symfony/recip
   es-contrib) will not be opinionated. Curated by the community at large, most
   submissions will be accepted.

Other than the rules to accept submissions, the "main" and the "contrib"
repositories work in the exact same way, except for the following differences:

 * Only the "main" repository can define aliases (that makes sense I suppose);

 * By default, Symfony Flex only searches recipes in the "main" repository.
   Using the "contrib" repository is *opt-in*. Enable it by executing `composer
   config extra.symfony.allow-contrib true`.

And packages can be promoted from the "contrib" repository to the "main" one as
well.

To maintain a high level of quality, I have also developed a set of validation
rules for recipes. When you submit a pull request on both repositories, the
Symfony Flex server runs a series of checks to ensure that your changes are
valid. Basic checks like validating the existence of the package on Packagist.
And more interesting ones like checking that two packages do not define the
same alias.

![Symfony Flex Pull Request Validation](/media/articles/2017-04-18-symfony4-contributing-recipes/flex-ci-ko.png)

But more interesting, a dedicated staging Flex Server is built for every pull
request. That allows anyone to test the changes before they are merged into the
master/production branch of a repository.

![Symfony Flex Pull Pull Request Staging Server](/media/articles/2017-04-18-symfony4-contributing-recipes/flex-ci-ok.png)

For instance, if you submit a pull request (number 42) on the "contrib"
repository that adds a recipe for package "foo/bar", install your new package
by defining the `SYMFONY_ENDPOINT` environment variable:

```bash
SYMFONY_ENDPOINT=https://symfony.sh/r/contrib/42 composer req foo/bar
```

If changes are submitted for "core" packages (like in
`symfony/framework-bundle`), using the environment variable even works with
`composer create-project`:

```bash
SYMFONY_ENDPOINT=https://symfony.sh/r/main/42 composer create-project symfony/skeleton demo
```

That's all for now. I hope that these last minute changes make you happy.
