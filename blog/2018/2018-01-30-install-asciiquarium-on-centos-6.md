---
title: Install Asciiquarium on Centos 6
author: svennd

date: 2018-01-30T14:08:42+00:00
url: /install-asciiquarium-on-centos-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - Asciiquarium
  - centos

---
I'm writing a "fun" script, and one of the fun commands it executes is [Asciiquarium][1]. This is my guide on how I got it working in Centos 6 🙂

First the dependecies :

<pre>yum install perl-Curses perl-ExtUtils-MakeMaker</pre>

If you miss one, you will get his error :

<pre>Can't locate ExtUtils/MakeMaker.pm in @INC</pre>

First we need Term::Animation, this can be installed using by downloading and _making_ it locally. Note that _make test_, will fail due to missing _Test::More_ in Centos, that's not a big deal, and it will work without it as well.

<pre>wget http://search.cpan.org/CPAN/authors/id/K/KB/KBAUCOM/Term-Animation-2.4.tar.gz
tar -zxvf Term-Animation-2.4.tar.gz
cd Term-Animation-2.4/
perl Makefile.PL && make
make install</pre>

After we got that installed, we can download ASCIIQuarium and install it :

<pre>wget http://www.robobunny.com/projects/asciiquarium/asciiquarium.tar.gz
tar -zxvf asciiquarium.tar.gz
cd asciiquarium_1.0/
cp asciiquarium /usr/games/
chmod 755 /usr/games/asciiquarium</pre>

![](/img/2018/01/fishy-1.png) 

And there we go running /usr/games/asciiquarium will give you a nice fish aquarium onscreen 🙂

 [1]: http://www.robobunny.com/projects/asciiquarium/html/