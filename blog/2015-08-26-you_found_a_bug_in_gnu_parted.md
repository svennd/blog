---
title: You found a bug in GNU Parted!
author: svennd

date: 2015-08-26T09:49:28+00:00
url: /you_found_a_bug_in_gnu_parted/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Heyho, I was not looking for a bug tho, so that's bad news 😀

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">You found a bug in GNU Parted! Here's what you have to do:
...
Assertion (bs != NULL) at ../../../../libparted/fs/fat/bootsector.c:282 in</pre>

As any bad user, I stopt reading there, to see what the internet had to say about this. Seems it was [reported already][1] (and solved!) so I was off the hook! However I tend to update&upgrade this system rather regular and somewhere I recalled I even installed an "auto updater" (unattended-upgrades, -redhat- yum-cron). So I knew my repo was not up-to-date with this release, I could wait for it, but I need this disk formatted right now. Since parted is open-source software, one can simply download the source and compile them self. (download from [gnu.org][2])   This machine is debian based,

<pre>svenn@work:~/parted-3.0$ cat /etc/debian_version
jessie/sid</pre>

(I have no idea what distribution, terminal is my GUI for this machine)

After a few attempts to compile I started collecting dependency's I did not have, so feel free to check if you have them before starting 🙂

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">sudo apt-get install uuid-dev libdevmapper-dev libncursesw5-dev libghc-readline-dev</pre>

I originally took parted 3.0.0 since it had a .tar.gz extension, that I know how to uncompress (tar xvf file.tar.gz) the latest version was .tar.xz so I thought it was to much bother to find a method... ([tar xvgJ file.tar.xz][3]) so I got this result for my laziness : (#overly_honest)

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">CC       freading.lo
In file included from freading.h:18:0,
                 from freading.c:20:
./stdio.h:1050:1: error: 'gets' undeclared here (not in a function)
 _GL_WARN_ON_USE (gets, "gets is a security hole - use fgets instead");</pre>

So I downloaded the 3.2.0 version (latest at time of writing), uncompressed and compiled. (<code class="EnlighterJSRAW" data-enlighter-language="null">./configure && make && make install</code>)  At-least this compiled and worked as expected, however the binary's "parted" now throw an error :

<pre>parted: error while loading shared libraries: libparted.so.2: cannot open shared object file: No such file or directory</pre>

So seemingly it does not find some library ... it probably means something is not there or not in the right place. However the /home/compile_dir/parted/parted binary just works oke, so I when with this version. As removing parted from the distribution was going remove allot dependency's I'm not sure I want to throw away just yet ...

Now on to the formatting of fat32 ! 🙂

 [1]: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=751406
 [2]: http://www.gnu.org/software/parted/
 [3]: http://stackoverflow.com/questions/12298368/how-to-extract-tar-xz-files-in-linux