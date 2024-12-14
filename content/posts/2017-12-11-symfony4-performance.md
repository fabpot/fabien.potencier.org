---
date: '2017-12-11'
title: 'Symfony 4: Performance out of the Box'
summary: |
    What about Symfony 4 performance? We worked on specific performance improvements for Symfony 4, but several non-performance related core changes help with performance as well.
params:
    author: Fabien Potencier
url: /symfony4-performance.html
---

Performance is an interesting and sensitive topic. Suffice to say that most projects should not care too much; modern PHP frameworks are fast enough for most use cases and projects. And PHP 7 performance improvements help a lot as well. But people like to compare frameworks, and I guess performance is one way to do so.

What about Symfony 4? During its development cycle, we did some nice performance optimizations: mainly to improve the router and container speed. But several non-related changes in Symfony 4 also help with performance... Your mileage may vary depending on your specific project of course.

Synthetic benchmarks that were made to back pull requests that claimed to improve performance do not always convert to gain on real-world projects. Now that Symfony 4 is out, it's time to test a full project. An "Hello World!" application benchmark is a good start as it helps understand **the base performance of a framework**. It helps understand the overhead added on top of PHP by your framework of choice. We spent so much time doing optimizations that I wanted to compare the out-of-the-box performance of a default Symfony Standard Edition project (based on Symfony 3) with the performance of a Symfony 4 project.

Benchmarking is a science and takes a lot of time. Luckily enough, and while I was procrastinating, someone did the work for me. The `http://www.phpbenchmarks.com/en` website has a great benchmark protocol and a great UI that let you browser and compare results easily.

According to those benchmarks, an "hello world" page on Symfony 4.0 is almost **twice as fast** as the same code using Symfony 3.4. You read that right. On PHP 7.2, Symfony 4.0 is twice as fast compared to Symfony 3.4.

There is also a benchmark for a more complex REST API application. Symfony 4 is again still much faster than 3.4.

Visit [http://www.phpbenchmarks.com/en](http://www.phpbenchmarks.com/en) to get more numbers. Among other things, you will learn the following:

* Symfony on PHP 7.2 makes your code quite a bit faster than 7.1;

* Symfony 2.3 is the [second fastest Symfony release](http://www.phpbenchmarks.com/en/comparator/compare.html?components=symfony-2.3%7Esymfony-2.4%7Esymfony-2.5%7Esymfony-2.6%7Esymfony-2.7%7Esymfony-2.8%7Esymfony-3.0%7Esymfony-3.1%7Esymfony-3.2%7Esymfony-3.3%7Esymfony-3.4%7Esymfony-4.0&benchmarkType=all&benchmarkTools=apache-bench&phpVersions=php-7.2&concurrencies=) since 2.0

* Symfony 3.4 is the slowest release since 2.0 (deprecated features are probably one of the reasons);

* Symfony 4.0 is almost three times faster as Laravel 5.5.

I would love to get some numbers for your application. Share them if you upgraded recently.

Enjoy using Symfony 4.0, the fastest Symfony ever released!
