---
title: Can’t locate ExtUtils/MakeMaker.pm in @INC Centos 7
author: svennd

date: 2016-03-23T14:13:04+00:00
url: /cant-locate-extutilsmakemaker-pm-in-inc-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Ow boy do we love dependencies. Well this one is part of perl-devel.

<pre>[root@server linux-kstat]# perl Makefile.PL
Can't locate ExtUtils/MakeMaker.pm in @INC (@INC contains: /usr/local/lib64/perl5 /usr/local/share/perl5 /usr/lib64/perl5/vendor_perl /usr/share/perl5/vendor_perl /usr/lib64/perl5 /usr/share/perl5 .) at Makefile.PL line 1.
BEGIN failed--compilation aborted at Makefile.PL line 1.
</pre>

Fixing it :

<pre>yum install perl-devel</pre>

on a rather clean system this installed :

<pre>=========================================
 Package 
=========================================
Installing:
 perl-devel 
Installing for dependencies:
 gdbm-devel 
 libdb-devel 
 perl-ExtUtils-Install 
 perl-ExtUtils-MakeMaker 
 perl-ExtUtils-Manifest 
 perl-ExtUtils-ParseXS 
 perl-Test-Harness 
 pyparsing 
 systemtap-sdt-devel 

Transaction Summary
=========================================
Install 1 Package (+9 Dependent packages)</pre>

happy building!

note : as I found out later, you don't need the "complete" perl-devel, but large chance you will get other errors, that require one of the packages installed by perl-devel. For the pure requirement try :

<pre>yum install perl-ExtUtils-MakeMaker</pre>

Another option is installing this library (the latest version) using cpan : (obv. this requires a working CPAN)

<pre>cpan -i ExtUtils::MakeMaker</pre>

Feel free to leave a thanks 😉