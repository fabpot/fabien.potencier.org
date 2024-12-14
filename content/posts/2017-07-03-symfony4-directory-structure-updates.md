---
date: '2017-07-03'
title: 'Symfony 4: Directory Structure Updates'
summary: |
    After a long discussion in the community, the directory structure was slightly changed.
params:
    author: Fabien Potencier
url: /symfony4-directory-structure-updates.html
---

After [a long discussion in the
community](https://github.com/symfony/flex/issues/56), the directory structure
has been slightly changed.

`etc/` was renamed to `config/`, and `web/` to `public/`. My blog posts about
Symfony 4 have just been updated to reflect these changes.

If you already have a project using Symfony Flex, upgrading is as simple as:

 * Renaming `etc/` to `config/`;
 * Renaming `web/` to `public/`;
 * Updating your web server configuration to take into account the new `public/` directory;
 * Upgrading `symfony/flex` to the latest version;
 * Done.
