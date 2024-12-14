---
date: '2009-08-25'
title: Developers should be Artists
summary: |
    During my holidays, I took the chance to step back a little and think about my
    day-to-day work. In this post, I try to explain why hacking is really an art.
params:
    author: Fabien Potencier
url: /developers-should-be-artists.html
aliases:
  - /developers-should-be-artists
  - /article/32/developers-should-be-artists
---

During my holidays, I took the chance to step back a little and think about my
day-to-day work. As the CEO of [Sensio](http://www.sensiolabs.com/), I have
the opportunity to do a lot of different jobs. But what I really like to do is
**hacking**. By hacking, I mean developing software for the fun of it,
software that is not for a "paying" customer. And as such, hacking cannot be a
day job per se. Hacking is something you do on the side. It's a hobby, a
passion. And Open-Source is probably the best way to share your hacks.

Why do I like hacking so much? Obviously because I'm *free* to do whatever I
want, with the tools and the methodology I choose. But more important, because
I have the freedom to do it *iteratively* with no schedule pressure. Building
a software is a long iterative process. I love to be able to code something,
forget about it for some time, and come back to it later on to enhance it, or
throw it away altogether. But back to the artist side of things. How much time
do you think Leonardo da Vinci took to paint the Mona Lisa? According to
[Wikipedia](http://en.wikipedia.org/wiki/Mona_Lisa), he began painting the
Mona Lisa in 1503 and he is thought to have finished it shortly before he died
in 1519. What? 13 years for just one painting? That's insane. Perhaps, but
that's the way it works for artists. I started developing symfony in 2004, and
it still unfinished in 2009, and probably will never be (and the number of
features has not raised that much in five years). Hacking is an art, and
hackers should act as artists. Hackers and painters have a lot in common but I
won't talk about the analogy too much as Paul Graham wrote an excellent
[essay](http://www.paulgraham.com/hp.html) and a whole
[book](http://www.paulgraham.com/hackpaint.html) on this topic. I recommend
you to read both of them if you are a hacker or a developer.

Hacking is about creating something by using the computer as a medium of
expression. One of the best hacker and artist I know of is [**Why the lucky stiff**](http://en.wikipedia.org/wiki/Why_the_lucky_stiff).
From the beautiful camping micro-framework, to the excellent "Poignant Guide
to Ruby" book, he was very prolific and a real artist. I use the past tense,
as he recently [removed](http://ejohn.org/blog/eulogy-to-_why/) all his online
presence.

Everyday, I try to learn new ways to write beautiful code in PHP (who's
laughing at me?). You can easily follow my progress by watching the evolutions
of the symfony code, and more recently the
[symfony components](http://components.symfony-project.org/) code.

<div style="float: right; padding: 10px">
  <img src="/media/articles/stos-punch.jpg" alt="Stos-Punch from ST Magazine page 146" />
</div>

If you are looking for more "programming art", I also did some attempts with
[Twitto](http://twitto.org/) and [Twittee](http://twittee.org/), my two
Twitter experiments.


```php
// Twitto
require __DIR__.'/c.php';
if (!is_callable($c = @$_GET['c'] ?: function() { echo 'Woah!'; }))
  throw new Exception('Error');
$c();

```

-


```php
// Twittee
class Container {
 protected $s=array();
 function __set($k, $c) { $this->s[$k]=$c; }
 function __get($k) { return $this->s[$k]($this); }
}

```

Back in the early nineties, I did some similar experiments. At that time, the
goal was to do as much as possible in [10 lines of code](/media/articles/stos-punch-big.jpg). I was once published
in the French ST Magazine (August 1990) for a program done is STOS (you can
see the wonderful program in the sidebar).

If you ask me, a hacker should definitely be an artist. But can a developer
working for a company be an artist? Unfortunately, I don't think so. To be an
artist, you need time and freedom, two essential things you rarely have when
working for a customer.
