---
date: '2009-06-09'
title: Quick SSH Tip
summary: |
    If you need to regularly connect to a lot of different servers like I do, you will probably enjoy this tip!
params:
    author: Fabien Potencier
url: /quick-ssh-tip.html
aliases:
    - /quick-ssh-tip
    - /article/19/quick-ssh-tip
---

If you need to regularly connect to a lot of different servers like I do, you
probably use SSH to connect to them, and you also probably use your personal
SSH identity file to ease the connection.

Some time ago, I re-discover a neat trick to simplify the connection by using
the `.ssh/config` file. I don't know why I forgot about it, but as it seems
that a lot of people around me do not know about this file either, here is a
small post on how it can be used to your advantage.

Let's say you have a host at `1.2.3.4` you need to connect to and the username
you need to use is `fabien`. Each time you want to connect to it, you need to
type something like the following:

    $ ssh fabien@1.2.3.4

Simple enough, even if you need to remember the remote user you need to use
for each server. However, if you need to specify a specific key, it becomes
more verbose:

    $ ssh -i /Users/fabien/keys/myserver.key fabien@1.2.3.4

That's difficult to remember, quite tedious to write and error prone. Instead,
I want to be able to just type:

    $ ssh myserver

It's quite easy. Create a `.ssh/config` file under your home directory and put
something like this inside:


```txt
Host myserver
  HostName 1.2.3.4
  User root
  IdentityFile /Users/fabien/keys/myserver.key

```

That's all there is to it. Now, connecting to the server is as easy as typing:

    $ ssh myserver

And it works everywhere SSH is involved. For instance, when you use `scp` to
copy a file:

    $ scp localfile.txt myserver:/tmp/

It is also a great way to not give sensitive information in configuration
files. For instance, in a symfony project, the `properties.ini` file can
contain the information to connect to the production servers used for
deployment. Instead of having to hardcode the real host name, the login and
the password like this:


```ini
[production]
host=www.myserver.com
port=22
user=someusername
pass=somepassword
dir=/var/www/mysite/

```

You can simply reference the name you gave in the `config` file and keep the
details secret on your local machine:


```ini
[production]
host=myserver
port=22
dir=/var/www/mysite/

```

Unix is really powerful thanks to little things like this one. By the way, if
you want to know more about the Unix history, you can
[read](http://www.computerworld.com/action/article.do?command=printArticleBasic&taxonomyName=Operating+Systems&articleId=9133570&taxonomyId=89)
the really interesting "Unix turns 40: The past, present and future of a revolutionary OS"
article published on ComputerWorld.com earlier this month.



