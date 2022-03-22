---
title: 'gssapi/gssapi.h: No such file or directory on Centos 7.6'
author: svennd

date: 2019-10-07T21:07:58+00:00
url: /gssapi-gssapi-h-no-such-file-or-directory-on-centos-7-6/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - mongodb

---
Library missing during install of MongoDB :

<pre class="EnlighterJSRAW" data-enlighter-language="null">gssapi/gssapi.h: No such file or directory</pre>

On Centos 7.6 it gets fixed by :

<pre class="EnlighterJSRAW" data-enlighter-language="null">yum install krb5-devel.x86_64</pre>

krb5 is a development package for kerberos, a network authentication system;

<pre class="EnlighterJSRAW" data-enlighter-language="null">Kerberos is a network authentication system. The krb5-devel package contains the header files and libraries needed for compiling Kerberos 5 programs. If you want to develop Kerberos-aware programs, you'll need to install this package.</pre>

&nbsp;