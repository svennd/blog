---
title: 'gem: Command not found'
author: svennd

date: 2018-01-19T12:26:01+00:00
url: /gem-command-not-found/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - gem
  - ruby

---
While installing a ruby package I hit the error :

<pre>gem install --version 1.15.4 bundler
make: gem: Command not found
make: *** [setup] Error 127</pre>

This can be resolved by installing the package :

<pre>yum -y install rubygems-devel</pre>

or for the Debian friends

<pre>apt-get install rubygems</pre>

Happy building 🙂