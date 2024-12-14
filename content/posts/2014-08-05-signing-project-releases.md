---
date: '2014-08-05'
title: Signing Project Releases
summary: |
    About a year ago, I started to sign all my Open-Source project releases. Here is how you verify the integrity of what you've installed in your project.
params:
    author: Fabien Potencier
url: /signing-project-releases.html
aliases:
    - /signing-project-releases
    - /article/73/signing-project-releases
---

About a year ago, I started to sign all my Open-Source project releases. I briefly mentioned it during my [SymfonyCon keynote in Warsaw](https://www.youtube.com/watch?v=qlrwfOL0230&list=UULdVmxwj9dQqM8tJJp2LYGw), but this post is going to give you some more details.

Whenever I release a new version of a project, I sign the Git tag with my [PGP key](http://pgp.mit.edu:11371/pks/lookup?search=0xeb8aa69a566c0795&op=vindex&fingerprint=on&exact=on): [`DD4E C589 15FF 888A 8A3D D898 EB8A A69A 566C 0795`](https://fabien.potencier.org/about).

Checking Git Tag Signatures
---------------------------

If you want to verify a specific release, you need to install PGP first, and then get my PGP key:

    $ gpg --keyserver pgp.mit.edu --recv-keys 0xeb8aa69a566c0795

Then, use `git tag` to check the related tag. Here is how to check the Symfony 2.4.2 tag (from a [Symfony clone](https://github.com/symfony/symfony)):

    $ git tag -v v2.4.2

Verification worked if the output contains the key used to sign the tag (`566C0795`) and contains a text starting with "Good signature from ...". Because of how Git works, having a good signature on a tag also means that all commits reachable from that tag are covered by this signature (that's why [signing all commits/merges](http://git.661346.n2.nabble.com/GPG-signing-for-git-commit-td2582986.html) is not needed.)

You can see the PGP signature by using the following command:

    $ git show --show-signature v2.4.2

For the curious ones, I'm going to take Symfony 2.4.2 as an example to explain how it works. First, Git does not sign the contents of a commit itself (which is empty anyway for tags), but its headers. Let's display the headers for the Symfony v2.4.2 tag:

    $ git cat-file -p v2.4.2

You should get the following output:

    object b70633f92ff71ef490af4c17e7ca3f3bf3d0f304
    type commit
    tag v2.4.2
    tagger Fabien Potencier <fabien.potencier@gmail.com> 1392233223 +0100

    created tag 2.4.2
    -----BEGIN PGP SIGNATURE-----
    Version: GnuPG v1.4.13 (Darwin)

    iF4EABEIAAYFAlL7ywcACgkQ64qmmlZsB5W1cAEAtZOVz5OT7i8vAEiLqnMYyM5n
    +XMbyTMVXyYfBqjqkmUA/AxAFTp7oTeHY3yepx/uuxF91+DOnvbxf4b2BqSCx0dq
    =sv1G
    -----END PGP SIGNATURE-----

The PGP signature is calculated on all lines up to the beginning of the signature:

    object b70633f92ff71ef490af4c17e7ca3f3bf3d0f304
    type commit
    tag v2.4.2
    tagger Fabien Potencier <fabien.potencier@gmail.com> 1392233223 +0100

    created tag 2.4.2

You can try it by yourself by saving those lines in a `test` file, and create a `test.sig` file with the PGP signature:

    -----BEGIN PGP SIGNATURE-----
    Version: GnuPG v1.4.13 (Darwin)

    iF4EABEIAAYFAlL7ywcACgkQ64qmmlZsB5W1cAEAtZOVz5OT7i8vAEiLqnMYyM5n
    +XMbyTMVXyYfBqjqkmUA/AxAFTp7oTeHY3yepx/uuxF91+DOnvbxf4b2BqSCx0dq
    =sv1G
    -----END PGP SIGNATURE-----

Then, check that the signature matches the Git headers with the following command:

    $ gpg --verify test.asc test

So, when signing a tag, you sign the commit sha1 (and so all reachable commits), but also the tag name (and so the version you expect to get).

Signing Github Archives
-----------------------

That's great, but when using Composer, you can get the code either as a Git clone (`--prefer-source`) or as an archive (`--prefer-dist`). If Composer uses the latter, you cannot use the signature coming from the tag, so how can you check the validity of what Composer just downloaded?

Whenever I make a new release, I also publish a file containing a sha1 for the zip file as returned by the Github API (`https://api.github.com/repos/XXX/XXX/zipball/VERSION`) but also a sha1 calculated on the file contents from the zip (the exact same files installed by Composer.) Those files are hosted on a [dedicated checksums](https://github.com/sensiolabs/checksums) repository on Github.

As an example, let's say I have a project using Symfony 2.4.2 (you can check the version installed by Composer by running `composer show -i`). The sha1s are available here: https://raw.githubusercontent.com/sensiolabs/checksums/master/symfony/symfony/v2.4.2.txt.

This file is signed, so you first need to verify it:

    $ curl -O https://raw.githubusercontent.com/sensiolabs/checksums/master/symfony/symfony/v2.4.2.txt
    $ gpg --verify v2.4.2.txt

Now, you can check the validity of the files downloaded and installed by Composer:

    $ cd PATH/TO/vendor/symfony/symfony
    $ find . -type f -print0 | xargs -0 shasum | shasum

The sha1 displayed should match the one from the file you've just downloaded (the one under the `files_sha1` entry.)

To make it easier, you can also check all your dependencies via a simple script provided in the repository. From your project root directory (where the `composer.json` file is stored), run the following

    $ PATH/TO/check-vendors.sh

It will output something along the lines of:

    symfony/swiftmailer-bundle@v2.2.6                        OK  files signature
    symfony/symfony@v2.5.2                                   KO  files signature
    twig/extensions@v1.0.1                                   OK  files signature
    twig/twig@v1.15.0                                        OK  files signature
    white-october/pagerfanta-bundle@dev-master               --  unknown package
    willdurand/hateoas@1.0.x-dev                             --  unknown package

     1 packages are potentially corrupted.
     Check that your did not add/modify/delete some files.

Consider the checksum feature as experimental and as such, any feedbacks would be much appreciated.



