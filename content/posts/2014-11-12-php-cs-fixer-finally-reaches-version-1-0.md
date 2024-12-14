---
date: '2014-11-12'
title: PHP CS Fixer finally reaches version 1.0
summary: |
    Let's celebrate version 1.0!
params:
    author: Fabien Potencier
url: /php-cs-fixer-finally-reaches-version-1-0.html
aliases:
    - /php-cs-fixer-finally-reaches-version-1-0
    - /article/76/php-cs-fixer-finally-reaches-version-1-0
---

A few years ago, I [wrote a small script](https://gist.github.com/fabpot/3f25555dce956accd4dd) to automatically fix some common coding standard mistakes people made in Symfony pull requests. It was after I got bored about all the comments people made on pull requests to ask contributors to fix their coding standards. As humans, we have much better things to do! The tool helped me fix the coding standard issues after merging pull requests and keep the whole code base sane. It was a manual process I did on a regular basis but it did the job.

After a while, I decided to [Open-Source the tool](https://github.com/FriendsOfPHP/PHP-CS-Fixer), like I do with almost all the code I write. I was aware of the limitation of the tool, the code was very rudimentary, but as Reid Hoffman said once: "If you are not embarrassed by the first version of your product, you've launched too late." To my surprise, people started to use it on their own code, found bugs, found edge cases, added more fixers, and soon enough, we all realise that using regular expressions for such things is doomed to fail.

Using the PHP tokens to fix coding standards is of course a much better approach, but every time I sat down to rewrite the tool, I got distracted by something that was more pressing. So, the tool stagnated for a while. The only real progress for Symfony was the introduction of [fabbot.io](http://fabbot.io/) which alerts contributors of coding standard issues before I merge the code.

The current stable version of [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) was released in August 2014 and it is still based on regular expressions, two years after the first public release. But in the last three months, things got crazy mainly because of Dariusz Ruminski. He did a great job at rewriting everything on top of a parser based on the PHP tokens, helped by 21 other contributors. After 13,000 additions and 5,000 deletions, I'm very proud to announce version 1.0 of PHP-CS-Fixer; it is smarter, it is more robust, and it has more fixers. Any downsides? Yes, speed; the tool is much slower, but it is worth it and enabling the new cache layer helps a lot.

As I [learned today on Twitter](https://twitter.com/fabpot/status/532575559474507776), a lot of people rely on the PHP CS Fixer on a day to day basis to keep their code clean, and that makes me super happy. You can use the fixer from some IDEs like PhpStorm, NetBeans, or Sublime. You can install it via Composer, a phar, homebrew, or even Grunt. And there is even a Docker image for it!



