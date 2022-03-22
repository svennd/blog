---
title: nodejs cannot update/install on centos 7 due to missing http_parser
author: svennd

date: 2017-08-28T14:10:05+00:00
url: /nodejs-cannot-updateinstall-on-centos-7-due-to-missing-http_parser/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - nodejs

---
While installing node.js :

<pre>Error: Package: 1:nodejs-6.11.1-1.el7.x86_64 (epel)
           Requires: libhttp_parser.so.2()(64bit)
Error: Package: 1:nodejs-6.11.1-1.el7.x86_64 (epel)
           Requires: http-parser &gt;= 2.7.0</pre>

You can solve this by installing http_parser manually :

<pre>yum install https://kojipkgs.fedoraproject.org//packages/http-parser/2.7.1/3.el7/x86_64/http-parser-2.7.1-3.el7.x86_64.rpm</pre>

The [reason][1] behind the issue, is in that RHEL 7.4 now includes this package in the default repo, and for that reason its now removed from _epel_ repository. The result is that there is no fight for RHEL users what version is best, but the disadvantage is that Centos users who are stuck for now on Centos 7.3, are missing a dependency for _node.js_ until Centos 7.4 is out that is.

 [1]: https://bugzilla.redhat.com/show_bug.cgi?id=1481470