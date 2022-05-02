---
title: 'binary : No such file or directory, 32 bit'
author: svennd

date: 2019-12-12T09:03:12+00:00
url: /binary-no-such-file-or-directory-32-bit/
thumbnail: /img/2020/05/pc_2.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - 64bit
  - centos

---
I got this seemingly wrong error message from Centos 8 Linux shell :

<pre>[root@server httpd]# ll
total 566
drwxrwxr-x  3 svennd svennd      3 Dec 11 16:23 apps
drwxrwxr-x 20 svennd svennd      20 Dec 11 16:23 exts
drwxrwxr-x  4 svennd svennd      10 Dec 11 16:23 lib
lrwxrwxrwx  1 svennd svennd      8 Dec 11 16:23 tclsh -&gt; tclsh8.4
-rwxrwxr-x  1 svennd svennd      781385 Dec 11 16:23 tclsh8.4
drwxrwxr-x  3 svennd svennd      5 Dec 11 16:23 work
[root@server httpd]# ./tclsh8.4
bash: ./tclsh8.4: No such file or directory
</pre>

It's there right ? So let's check whats going on with this "No such file".

<pre>[root@server httpd]# file tclsh8.4
tclsh8.4: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.9, with debug_info, not stripped</pre>

The issue is in the **32-bit** information you get here, all modern Linux distributions (here Centos 8) are 64-bit; By default they don't come with 32-bit library's; You could try to find out what is going on by using **ldd** & **strace.**

But in this case it gave little information :

<pre>[root@server httpd]# ldd ./tclsh8.4
        not a dynamic executable</pre>

The solution is to install glibc for i686 (32 bit);

<pre>yum install glibc-devel.i686</pre>

And voila, magically the error disappeared and I could run this piece of ancient history.
