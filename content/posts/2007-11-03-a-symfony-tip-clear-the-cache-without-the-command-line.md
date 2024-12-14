---
date: '2007-11-03'
title: 'A symfony tip: Clear the cache without the command line'
summary: |
    Learn how to clear the symfony cache without using the command line.
    This tip is quite useful if you only have FTP access to your production server.
params:
    author: Fabien Potencier
url: /a-symfony-tip-clear-the-cache-without-the-command-line.html
aliases:
    - /a-symfony-tip-clear-the-cache-without-the-command-line
    - /article/21/a-symfony-tip-clear-the-cache-without-the-command-line
---

If you follow the symfony mailing-lists or the symfony forums,
you already know the magic answer for all your symfony problems:

> Please, try to clear the cache!

It means you have to call the `clear-cache` task from the command line (or `cache:clear` in symfony 1.1):


``` bash
$ symfony clear-cache
```


...or use the `cc` shortcut:


``` bash
$ symfony cc
```


But what if you don't have access to the command line on your server?
What if you only have a FTP account?

The answer is amazingly quite simple.

All cache files are stored in a single `cache/` directory located in the project root directory.

So, to clear the cache, you can just remove all the files and directories under `cache/`.
And symfony is smart enough to re-create the directory structure it needs if it does not exist.


