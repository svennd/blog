---
title: Install APCUPSD on Centos 6
author: svennd

date: 2018-05-03T10:17:55+00:00
url: /install-apcupsd-on-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - apcupsd
  - centos

---
I can't seem to find apcupsd (a ups monitor, that shutdowns computers, before you run out of ups power) on Centos 6, surprisingly on Centos 7 this package is available from epel, but not so for Centos 6. Here is a condense howto get the source and compile it. (mainly for my personal use)

<pre>wget https://kent.dl.sourceforge.net/project/apcupsd/apcupsd%20-%20Stable/3.14.14/apcupsd-3.14.14.tar.gz
tar xvf apcupsd-3.14.14.tar.gz
cd apcupsd-3.14.14
./configure
make
make install</pre>

Then follow settings :

<blockquote class="wp-embedded-content" data-secret="GwWxeUUO2f">
  <p>
    [Install APCUPSD on Centos 7](https://www.svennd.be/install-apcupsd-on-centos-7/)
  </p>
</blockquote>