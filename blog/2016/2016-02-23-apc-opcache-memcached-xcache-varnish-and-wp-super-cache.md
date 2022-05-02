---
title: APC, Opcache, Memcached, XCache, Varnish and WP Super Cache
author: svennd

date: 2016-02-23T19:03:05+00:00
url: /apc-opcache-memcached-xcache-varnish-and-wp-super-cache/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
  - webdev

---
There are quit a few types of cache available to developers/sysadmins/maintainers, ... they however all serve the same purpose. A cache stores "recently" used information where it can be accessed "faster" then the original source. This is a rather vague description, caching happens on multiple levels and while memory caching is blazing fast compared to disk storage, sometimes it makes sense to store a cache on disk. When I talk to non-technical people and I say I'm comparing/searching cache methods, they don't understand why it takes so long to find the fastest/best method, that's why I wanne share what I already found, while this is not a complete list, its a summary of what I have found so far. My main focus is web-caching. If you know some other web-caches feel free to comment !

**Alternative PHP Cache (APC) **: APC is a open source framework that optimizes PHP intermediate code and caches data and compiled code from the PHP bytecode compiler in shared memory. The last part however only stands for older PHP versions (such as on Centos official repo). In newer versions of PHP (5.5+) APC has been deprecated for Opcache by Zend after they made it open source. APCu however remains available, this is a "user part", a custom application could specifically set variables to be stored in cache. Since I'm not really looking building code myself, APC is only a legacy caching method, since Opcache takes over the most important part.

**Zend Opcache :**

> OPcache improves PHP performance by storing precompiled script bytecode in shared memory, thereby removing the need for PHP to load and parse scripts on each request.  
> **- [PHP manual][1]**

Opcache stores PHP parsed bytecode in memory and when they need to be executed are going from Memory -> CPU -> output. The drawback is if you change PHP files the cache becomes invalid, although for a forum (phpBB) this is not a big deal. Opcache is a clear win and I don't see the point in not having it on for VPS or dedicated servers.

  ![Simple Zend Opcache / -legacy APC- Cache workings](/img//2015/07/25451416-1.jpg)

**Memcached :**

> Free & open source, high-performance, distributed memory object caching system, generic in nature, but intended for use in speeding up dynamic web applications by alleviating database load. Memcached is an in-memory key-value store for small chunks of arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.  
> **- front page [memcached.org][2]**

So what memcached does, is store key-value data in memory, this data can come from database or page rendering. While PHP bytecode caching such as APC and zend OPcache is mainly a single machine optimization/caching, Memcached is not per default, memcached can even be distributed, although that is not a requirement, its good to know it can scale well. The speedup with memcached will happen cause the database is not touched for data that can be cached. Although the speed gain is not clearly visible, a relational database such as MariaDB has relatively a large overhead for calls, caching those to limit database calls can gain allot of speed. Although it should be said, [memcached is not install and win.][3]

![Request that can use memcached data are way cheaper to proces, leaving resources to more difficult requests.](/img//2015/07/25451552-1.jpg)

Request that can use memcached data are way cheaper to proces, leaving resources to more difficult requests.

**XCache**: is also an open-source opcode cacher, coming from the Lighthttpd field, while the [project][4] is not dead, there are [questions if PHP 7][5] will ever be supported.

**Varnish**:

> Varnish is an HTTP accelerator designed for content-heavy dynamic web sites as well as heavily consumed APIs. In contrast to other web accelerators, such as Squid, which began life as a client-side cache, or Apache and nginx, which are primarily origin servers, Varnish was designed as an HTTP accelerator. Varnish is focused exclusively on HTTP, unlike other proxy servers that often support FTP, SMTP and other network protocols.  
> **[Wikipedia][6]**

&nbsp;

I am not sure how Varnish works, from what I gather it should work on dynamic websites, but what I gather from [this article][7] it should only work with anonymous visitors. While that's great, most of visitors aren't actually anonymous but registered users... It has a good PR team, and since really high-end websites work with it, it must be good, but how good will it do on a VPS ? Only experimentation will tell I guess.

**Others**

On top of that my application (phpBB) also caches, I'm not sure what the cache is being used for, would it make sense to store this cache on a **memdisk** ? For this forum there are a few plugins such as **WP Super Cache** to name one.... Its seems to go on and on...

 [1]: http://php.net/manual/en/intro.opcache.php
 [2]: http://memcached.org
 [3]: https://groups.drupal.org/node/306033
 [4]: https://groups.google.com/forum/#!forum/xcache
 [5]: https://groups.google.com/forum/#!topic/xcache/xgEnMwcqpZQ
 [6]: https://en.wikipedia.org/wiki/Varnish_(software)
 [7]: https://dev.acquia.com/blog/explaining-varnish-beginners
