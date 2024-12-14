---
date: '2017-05-02'
title: 'Symfony 4: A quick Demo'
summary: |
    Eager to test Symfony 4. At least, the current preview version? Let's do it
    now.
params:
    author: Fabien Potencier
url: /symfony4-demo.html
---

Time to test Symfony 4... or at least let's test the experience of developing
Symfony 4 projects with Symfony 3.3. Keep in mind that all the tools are in
preview mode. Features might evolve over time. I'm waiting for your feedback!
The first stable version of Symfony Flex will not be released before Symfony 4
at the end of November 2017. It gives the community plenty of time to discuss
the changes I have described in this series of blog posts.

Creating a new Project
----------------------

The first step is to create a project. Currently, this needs to be done via
`composer create-project`. We might release a tool to bootstrap a project
faster.

Let's go:

```bash
composer create-project "symfony/skeleton:^3.3" demo
cd demo
```

`3.3` is the only currently available version and uses the yet-to-be-released
Symfony 3.3. Versions like , `4.0`, `lts`, or `latest` will be available
later on (but not before the [release of Symfony 4.0](https://symfony.com/roadmap?version=4.0#checker)
of course).

The command downloads the [Symfony
skeleton](https://github.com/symfony/skeleton) which consists of just one
`composer.json` file.

Then, it extracts the file into the `demo` directory and automatically runs
`composer install`. `symfony/flex` is the first package to be installed so that
it can hook into the Composer process. When Composer installs (or updates)
other dependencies, Symfony Flex looks for an associated *recipe* on the
Symfony Flex server, and executes it. You can see Symfony Flex in action via
the logs it adds to the Composer output.

![Symfony Flex Output](/media/articles/2017-05-02-symfony4-quick-demo/flex-output.png)

When finished, you should see a "What's next?" section that explains the
possible next steps, like running `make serve` to start the PHP built-in web
server. Before going further, go to the project's directory: `cd demo`.

![What's next?](/media/articles/2017-05-02-symfony4-quick-demo/whats-next.png)

`serve` is one of the tasks added to the local `Makefile`, as described in the
[`symfony/framework-bundle`](https://github.com/symfony/recipes/tree/master/symfony/framework-bundle/3.3)
recipe.

Note that some commands were automatically installed and run at the end of the
process:

![Scripts](/media/articles/2017-05-02-symfony4-quick-demo/scripts.png)

Again, those scripts were added to the project's `composer.json` file as
defined by the `symfony/framework-bundle` recipe. The second script did not run
because the console tool (available via `symfony/console`) is not installed by
default (we will see how to "fix" this issue later on).

Now is a good time to initialize Git:

```bash
git init
git add .
git commit -m "initial set of files"
```

Using `git add .` works well as Symfony took care of creating a "good"
`.gitignore` file.

Remember that the skeleton only has one file, `composer.json`. Check the
`demo/` directory now; some more files have been created. Those files were
added by Symfony Flex based on the installed packages, as described in recipes.

Let's examine the directory structure now. Most files has been added because of
the `symfony/framework-bundle` dependency.

The `.env` file defines the `APP_ENV` and `APP_DEBUG` environment variables:

```ini
###> symfony/framework-bundle ###
APP_ENV=dev
APP_DEBUG=1
APP_SECRET=ODtvoHW6pn3bE2rHgTa2MjkjwefzlsJH
###< symfony/framework-bundle ###
```

The comments allows Symfony Flex to "manage" this section. This is useful when
those variables needs to be removed when the package is removed. If you remove
the comments, Symfony Flex will not be able to automatically manage these
variables anymore. Have a look at `.gitignore` for a similar example.

If you're curious, check `public/index.php`, the new web front controller.

The most interesting files are under `config/`. The main entry points are the
empty `container.yaml` and `routing.yaml` files; this is where you can add
services, parameters, and routes for your project. A default configuration has
been installed as well for `symfony/framework-bundle` under `config/packages/`.
Feel free to tweak installed configuration files or add more for your own needs.

Last, but not least, `FrameworkBundle` is now registered in `bundles.php`:

```php
return [
    'Symfony\Bundle\FrameworkBundle\FrameworkBundle' => ['all' => true],
];
```

Even if a bundle does not have a recipe, Symfony detects Composer packages with
the `symfony-bundle` type and automatically enable them for all environments.
This avoids the creation of recipes when registration is just a matter of
enabling the bundle.

The `src/` directory is where you can store your PHP classes. Under the `App\`
namespace as registered in `composer.json`. Note that this is where
`Kernel.php` was also installed as `App\Kernel`.

Now, it is time to install some new dependencies via Composer. Let's start by
adding a more powerful web server for your project:

```bash
composer req webserver
```

And install Symfony console support via:

```bash
composer req cli
```

**Note**: `req` is a shortcut for `require` (the Composer CLI supports any
shortcut that is not ambiguous; use `rem` to remove a Package).

`webserver` is an **alias** for `symfony/web-server-bundle`, and `cli` is an
alias for `symfony/console`. That works because Symfony Flex knows how to
convert those aliases into full package names: `cli` is equivalent to
`console`, which is equivalent to `symfony/console`. The `symfony/` prefix is
always optional. Try `composer req workflow` or `composer req ldap`.
`web-server-bundle` is too cumbersome, so use `webserver` or just `server`.

For Symfony dependencies, Symfony Flex also recognizes a few more versions than
the usual Composer ones, mainly `next`, `previous`, `lts`, and `stable` (they
don't all work yet though).

```bash
composer req cli:next
```

After executing `composer req cli`, notice how the `assets:install` command
automatically ran. The `bin/console` file has also been added to your project.

![Scripts with Console](/media/articles/2017-05-02-symfony4-quick-demo/scripts-with-console.png)

Aliases work when removing dependency as well:

```bash
composer rem cli
```

... which also removes the `bin/console` binary (for fun, try this: `composer
rem framework-bundle`).

Remember I wrote about the developer experience when installing a Symfony
bundle? Let's go wild and install something really "complex" like an admin
generator based on Doctrine. How many steps to make it work? It might be fewer
than you expect, and definitely more fun.

First, let's install [EasyAdminBundle](https://github.com/javiereguiluz/EasyAdminBundle):

```bash
composer req admin
```

Besides installing the admin generator bundle, it also installs all its
transitive dependencies and auto-configures them all: `TwigBundle`,
`SecurityBundle`, `FrameworkExtraBundle`, and `DoctrineBundle`.

`admin` is a "generic" word. This is why I wrote about Symfony Flex recipes
being opinionated. There can only be one package aliased to `admin`. `orm` is
another generic word that is currently aliased to the Doctrine ORM.

Run the PHP built-in web-server via `make serve` or `bin/console server:start`
and go to `http://localhost:8000/admin/`. You should get an error as no
Doctrine entities exist yet. Let's create one in `src/Entity/Product.php`:

```php
namespace App\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 * @ORM\Table(name="product")
 */
class Product
{
    /**
     * @ORM\Column(type="integer")
     * @ORM\Id
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    public $id;

    /**
     * @ORM\Column(type="string", length=100)
     */
    public $name;

    /**
     * @ORM\Column(type="decimal", scale=2)
     */
    public $price;

    /**
     * @ORM\Column(type="text")
     */
    public $description;
}
```

Tweak the database configuration in `.env`:

```text
###> doctrine/doctrine-bundle ###
DB_URL=mysql://root@127.0.0.1:3306/symfony?charset=utf8mb4
###< doctrine/doctrine-bundle ###
```

Run the following commands to initialize the database:

```bash
./bin/console doctrine:database:create
./bin/console doctrine:schema:update --force
```

Finally, add the `Product` entity to the list of entities managed by the admin
generator (`config/packages/easy_admin.yaml`):

```yaml
easy_admin:
    entities:
        - App\Entity\Product
```

Try `http://localhost:8000/admin/` again. If everything worked well, you should
be able to manage products.

![Admin Generator Interface](/media/articles/2017-05-02-symfony4-quick-demo/admin.png)

Instead of installing an admin generator, have a look at this small screencast
where I'm using the `api` alias to bootstrap an API project quickly and easily:

<iframe width="560" height="315" src="https://www.youtube.com/embed/o9N1nOYfAl4" frameborder="0" allowfullscreen></iframe>

Here are some nice aliases you can try:

 * `sec-checker` to install the SensioLabs Security Checker;
 * `req-checker` to install the Symfony requirements checks;
 * `log` to install MonologBundle and all its dependencies;
 * `template` for Twig;
 * `mailer` for Swiftmailer;
 * `profiler` for the profiler;
 * ... you get the point :)

As `symfony/symfony` is not required anymore, you get more fine-grained
dependencies, but it might be cumbersome to install each Symfony component one
by one. To ease the pain, I'm experimenting with a new concept: "packs". A pack
is nothing more than a regular Git repository registered on Composer that
contains a `composer.json` that references a set of related packages. As an
example, I have created a [`debug-pack`
pack](https://github.com/symfony/debug-pack) that can be installed via
`composer req debug-pack`. Have a look at the [ORM
pack](https://github.com/symfony/orm-pack) or the [API
pack](https://github.com/api-platform/api-pack). Imagine a `web-pack` that
references nothing in `composer.json` but is associated with a recipe that
installs a set of default files under `public/` like favicons, a `robots.txt`,
and so on. Nice? Imagine the same for Travis, Blackfire, or Docker. Or a pack
that installs the exact same dependencies as the current Symfony Standard
Edition? Your imagination is the limit. Compose your applications with
off-the-shelf packages, packs, and their associated recipes.

Symfony Flex enables **distribution composition instead of inheritance**. The
new way is easier, more flexible, and more powerful at the same time.

The first version of the Symfony Flex server is quite simple, but over time,
more features will be implemented.

Now, it is probably time for you to write some code. What about some
controllers and templates? Hold on. Even if you can do what you are used to,
Symfony 3.3 and Symfony 4.0 proposes a much smoother experience that you might
like better. This is a great topic for my next post about Symfony 4.

Remember that the recipes repositories at
[https://github.com/symfony/recipes](https://github.com/symfony/recipes) and
[https://github.com/symfony/recipes-contrib](https://github.com/symfony/recipes-contrib)
are also public now. Feel free to look around. And keep in mind that
what you see is experimental. Current choices might change.
