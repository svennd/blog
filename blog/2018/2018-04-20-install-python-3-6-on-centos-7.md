---
title: Install Python 3.6 on Centos 7
author: svennd

date: 2018-04-20T09:07:41+00:00
url: /install-python-3-6-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - python

---
Sometimes you want to have newer software on Centos/Red Hat. But its because that slower adoption, we can actually use Centos for so long, and the support they deliver is amazing. That being said, sometimes there is no getting back and you need newer software. Python 3X is such a package. So let's go ahead and install Python 3.6 using [_iuscommunity_][1], (**I**nline with **U**pstream **S**table)

Some developmental tools to compile software from source code :

<pre>yum install yum-utils groupinstall development</pre>

Add the repository to our pool, then update the repos :

<pre>yum install https://centos7.iuscommunity.org/ius-release.rpm
yum update</pre>

and install python 3.6

<pre>yum install python36u python36u-pip</pre>

This adds python 3.6.4 at this time; which can be run :

<pre>python3.6</pre>

This can be used to install useful tools such as [NanoPlot][2] 🙂

<pre>pip3.6 install NanoPlot</pre>

That's it 🙂

 [1]: https://ius.io
 [2]: https://github.com/wdecoster/NanoPlot