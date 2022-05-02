---
title: 'Yum error: rpmdb : failed: BDB1507 Thread died in Berkeley DB library'
author: svennd

date: 2016-11-01T10:15:09+00:00
url: /yum-error-rpmdb-failed-bdb1507-thread-died-in-berkeley-db-library/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Wuuut, yum broke on my Centos 7 container ! For me this error :

<pre>error: rpmdb: BDB0113 Thread/process 10680/140044063065920 failed: BDB1507 Thread died in Berkeley DB library
error: db5 error(-30973) from dbenv-&gt;failchk: BDB0087 DB_RUNRECOVERY: Fatal error, run database recovery
error: cannot open Packages index using db5 -  (-30973)
error: cannot open Packages database in /var/lib/rpm
CRITICAL:yum.main:</pre>

Happened cause I ran out of memory, not completely OOM, but close enough to be an issue. (I had ~60mb free) I could not run any yum command, I tried _yum clean all _but that failed, also _rpm -rebuilddb_ did not work. In the end I had to manually remove the databases and rebuild from scratch.  (it sounds worse than it is)

So first I moved the databases to a temp location

<pre>mv /var/lib/rpm/__db* /tmp</pre>

(I had 3, .001-.003) Then I did a clean all, that worked, and I finished off doing an update, that did quit allot of updates. (woops)

<pre>yum clean all
yum update -y</pre>

After I verified everything worked I cleaned up my tmp :

<pre>rm -i /tmp/__db*</pre>

Extra tip, finding what processess use most memory :

<pre>top -o %MEM</pre>

(mysql and php where the biggest RAM eaters)