---
title: crouton add-apt-repository command not found
author: svennd

date: 2015-07-14T20:33:56+00:00
url: /crouton-add-apt-repository-command-not-found/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
tags:
  - chromebook
  - crouton
  - ubuntu

---
While working with my [crouton][1]/[chromebook][2]/[toshiba cb30-102][3](dutch), I hit upon a weird one. While try'ing to install a PPA. I got this error :

<code class="EnlighterJSRAW" data-enlighter-language="null">sudo: add-apt-repository: command not found</code>

Since my crouton is a default Ubuntu installation (12.04 LTS) with Xfce, I was stunned, since this is a 'default' command, but seemingly they install a minimal image, and this doesn't include all packets. So the fix is easy as pie :

<code class="EnlighterJSRAW" data-enlighter-language="null">sudo apt-get install python-software-properties</code>

Note for newer versions (12.10+) this might be the package name :

<code class="EnlighterJSRAW" data-enlighter-language="null">sudo apt-get install software-properties-common</code>

[source][4]

 [1]: https://github.com/dnschneid/crouton
 [2]: https://www.google.be/chrome/devices/chromebooks.html
 [3]: http://tweakers.net/productreview/101072/toshiba-cb30-102.html
 [4]: https://muffinresearch.co.uk/ubuntu-add-apt-repository-command-not-found/
