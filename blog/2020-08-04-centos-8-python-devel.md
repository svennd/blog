---
title: 'Centos 8 : python-devel'
author: svennd

date: 2020-08-04T14:08:28+00:00
url: /centos-8-python-devel/
thumbnail: /img/2020/04/centos.jpg
categories:
  - SysAdmin

---
You might find when you receive this error :

`fatal error: Python.h: No such file or directory`

The solution on Centos is to install python-devel package; In Centos 8 however this package was renamed due to the fact python 2.7 is no longer the default one, it has become 3. And this package was named w/o any suggestions;

<pre>[root@serverexamples]# yum install python-devel
Last metadata expiration check: 2:41:52 ago on Tue 04 Aug 2020 01:19:19 PM CEST.
No match for argument: python-devel
Error: Unable to find a match: python-devel</pre>

The solution is to add python version number :

`yum install python3-devel`