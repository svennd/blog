---
title: 'error: Python.h : No such file or directory'
author: svennd

date: 2017-01-18T13:56:19+00:00
url: /error-python-h-no-such-file-or-directory/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Somewhere along the way running python's helper pip, this message pop'd up :

<pre>error: Python.h : No such file or directory</pre>

save a google :

<pre>yum install python-devel</pre>

on Centos 6 this solved my tiny issue 🙂

&nbsp;

// edit for Ubuntu :

<pre>apt-get install python-dev libpython3-dev</pre>

&nbsp;