---
title: 'Error while loading shared libraries: libstdc++.so.6'
author: svennd

date: 2016-01-22T14:15:08+00:00
url: /error-while-loading-shared-libraries-libstdc-so-6/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
When installing a 32bit application you get the weirdest problems.

<pre>/opt/firebird/bin/gsec: error while loading shared libraries: libstdc++.so.6: cannot open shared object file: No such file or directory</pre>

Good thing repo's are available to solve most of life's problems :

<pre>yum install libstdc++.i686</pre>

(on a 64 bit machine/OS)