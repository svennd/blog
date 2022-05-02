---
title: 'fatal error: openssl/aes.h: No such file or directory'
author: svennd

date: 2017-01-21T13:58:09+00:00
url: /fatal-error-opensslaes-h-no-such-file-or-directory/
thumbnail: /img/2020/05/edward-howell-OEfm0hrY_rw-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin

---
During the pip installation of cryptography library I hit upon this missing library :

<pre>fatal error: openssl/aes.h: No such file or directory</pre>

In Centos 6 this is what you need :

<pre>yum install openssl-devel</pre>

&nbsp;