---
title: What Files Are In a RPM Package?
author: svennd

date: 2015-10-08T10:49:59+00:00
url: /what-files-are-in-a-rpm-package/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
> rpm -qlp fil.rpm

Source: _[What Files Are In a RPM Package?][1]_

Another useful tool  from package yum-utils is repoquery. Even non-installed or downloaded packages can be inspected this way :

<pre># install the tool
yum install yum-utils

# check what is in yum-utils package
repoquery -l yum-utils

# peek inside the nano package
repoquery -l nano</pre>

// update 13/01/2016 - I added repoquery, handy tool !

 [1]: http://www.cyberciti.biz/faq/howto-list-find-files-in-rpm-package/