---
title: 'Centos : could not find an LDAP library'
author: svennd
date: 2015-03-10T08:55:14+00:00
url: /centos-could-not-find-an-ldap-library/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I was trying to compile Apache from source, and got this error :

<pre>configure: error: could not find an LDAP library</pre>

This can be resolved by installing openldap-devel.

<pre>yum install openldap-devel</pre>

&nbsp;

&nbsp;
