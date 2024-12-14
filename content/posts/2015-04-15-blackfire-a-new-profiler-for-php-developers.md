---
date: '2015-04-15'
title: Blackfire, a new Profiler for PHP Developers
summary: |
    The one where I talk about my latest project: Blackfire.io
params:
    author: Fabien Potencier
url: /blackfire-a-new-profiler-for-php-developers.html
aliases:
    - /blackfire-a-new-profiler-for-php-developers
    - /article/77/blackfire-a-new-profiler-for-php-developers
---

<a href="https://blackfire.io/">
    <img src="/media/articles/blackfire_primary_square.png" style="width:150px; float: right; margin: 15px" />
</a>

I've always been fascinated by debugging tools; tools that help you understand what's going on in your code. In the Symfony world, the web debug toolbar and the web profiler are tools that gives a lot of information about HTTP request/response pairs (from exceptions to logs, submitted forms and even an event timeline), but it's only available in development mode as enabling those features in production would have a too significant performance impact. The Symfony profiler is also more about giving metadata about the code execution and less about what is executed.

If you want to understand which part of your code is executed for any given request, and where the server resources are spent, you need special tools; tools that instrument your code at the C level. The oldest tool able to do that is [XDebug](http://xdebug.org/) and a few years ago, Facebook also open-sourced XHProf. Both XDebug (as a profiler) and XHProf are profilers; they are able to answer a lot of questions you might have about the performance of your code, and they can help you understand why your code is slow.

But even if tools are available, performance monitoring in the PHP world is not that widespread. You are probably writing unit tests for your applications to ensure that you don't accidentally deploy broken features and to avoid regressions when you are fixing bugs. But what about performance? A broken page is a problem, but what about a page that takes seconds to display? **Less performance means less business.** So, continuously testing the performance of your applications should be a critical part of your development workflow.

Enter [Blackfire](https://blackfire.io/). Blackfire is a PHP profiler that simplifies the profiling of an app as much as possible.

The first big difference with existing tools is the [installation process](https://blackfire.io/getting-started); we've made it straightforward by providing easy-to-follow instructions for a lot of different platforms and Blackfire is even included by default on some major PHP cloud providers.

Once installed, profiling an HTTP request is as easy as it can get: use the [Google Chrome extension](https://blackfire.io/doc/web-page) to profile web pages from your browser, or use the command line tool to profile [web services, APIs](https://blackfire.io/doc/web-services), [PHP CLI scripts](https://blackfire.io/doc/cli-tutorial), or even [long-running scripts like daemons or workers](https://blackfire.io/doc/manual-instrumentation).

The other major difference with the other existing tools comes from the fact that Blackfire is a SaaS product.  It let us do a lot of things that would not be possible otherwise like storing the history of your profiles, making [comparisons between two profiles](https://blackfire.io/profiles/compare/362e9ecd-6b3c-41ce-b7cd-61e10cc71143/graph) really easy or providing a rich and interactive UI that evolves on a day-to-day basis.

If you've used XHProf in the past, you might wonder if it would make sense for you to upgrade to Blackfire. First, and unlike a popular belief, the current Blackfire PHP extension is not based on the XHProf code anymore. Starting from scratch helped us lower the overhead and structure the code for extensibility.

Then, and besides the "better experience", Blackfire offers some unique features like:

 * Profile your applications without changing a single line of code;
 * Easily focus on code you need to optimize thanks to more accurate results, aggregation, and smart cleaning of data;
 * More information about CPU time and I/O time;
 * No performance impact on the production servers when not using the profiler;
 * SQL statements and HTTP calls extraction;
 * [Team profiling](http://blog.blackfire.io/teams.html);
 * [Profile sharing](http://blog.blackfire.io/profiles-public-sharing.html)
 * an API;
 * [Garbage collector information](http://blog.blackfire.io/performance-impact-of-the-php-garbage-collector.html);
 * The soon-to-be-announced [Windows support](http://blog.blackfire.io/early-windows-support.html);
 * And much more...

We are very active on our [blog](http://blog.blackfire.io/) where you can learn more about the great features we are providing for developers and companies.

Blackfire has been in public beta for four months now and the response has been amazing so far. More than 20.000 developers have already signed up. You can read some [user feedback](https://twitter.com/blackfireio/favorites) on our [Twitter account](https://twitter.com/blackfireio), and some of them even wrote about their experience on the Blackfire blog: I recommend the article from [ownCloud](http://blog.blackfire.io/owncloud.html) as they did a lot of performance tweaks to make their code run faster thanks to Blackfire.

My mission with Blackfire is to give developers the best possible profiler for their applications. [Try it out](https://blackfire.io/) today for free and tell me what you think!



