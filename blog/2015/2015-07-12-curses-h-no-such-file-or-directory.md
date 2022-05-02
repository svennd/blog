---
title: 'curses.h: No such file or directory'
author: svennd

date: 2015-07-12T10:12:45+00:00
url: /curses-h-no-such-file-or-directory/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - librarys
  - samtools

---
While installing the latest samtools, there was a "missing" library called _[curses][1]_ this is needed for the command line visualization tview (text view) of samtools. Although there is a [build-in option][2] to not use this library. Its an easy fix :  (centos 6.6)

<pre>yum install ncurses-devel ncurses</pre>

for debian distro's this should work :

<pre>apt-get install libncurses5-dev libncursesw5-dev</pre>

&nbsp;

![tview, an example of samtools](/img//2015/07/29732960-1.png)


// update 22/01/2016

Similar issue on Centos 6 while installing Firebird. I had to install the static libs.

<pre>[root@gringott ]# yum search ncurses
ncurses.x86_64 : Ncurses support utilities
ncurses-devel.i686 : Development files for the ncurses library
ncurses-devel.x86_64 : Development files for the ncurses library
ncurses-libs.i686 : Ncurses libraries
ncurses-libs.x86_64 : Ncurses libraries
ncurses-static.x86_64 : Static libraries for the ncurses library
ncurses-base.x86_64 : Descriptions of common terminals
ncurses-term.x86_64 : Terminal descriptions</pre>

A catch 'm all for me :

<pre>yum install ncurses-static.x86_64 ncurses-libs.x86_64 ncurses-libs.i686 ncurses-devel.x86_64 ncurses-devel.i686</pre>

&nbsp;

 [1]: https://en.wikipedia.org/wiki/Curses_(programming_library)
 [2]: https://github.com/samtools/samtools/blob/develop/INSTALL
