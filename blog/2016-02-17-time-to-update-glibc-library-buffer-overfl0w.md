---
title: Time to update! Glibc library buffer overfl0w
author: svennd

date: 2016-02-17T15:34:15+00:00
url: /time-to-update-glibc-library-buffer-overfl0w/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
An important bug was discovered in the _glibc_ library, no fancy name -yet- like [GHOST][1], [heartbleed][2] or [shellshock][3] this time, it listens to [CVE-2015-7547][4].

As a CentOS user myself, updating is easy, -and largely automated- but still be sure to check, [cause the impact is great for this one][5]!

<pre>yum update glibc
</pre>

Today I got these :

<pre>Updating:
 glibc      2.17-106.el7_2.4      
Updating for dependencies:
 glibc-common   2.17-106.el7_2.4 
 glibc-devel   2.17-106.el7_2.4    
 glibc-headers  2.17-106.el7_2.4</pre>

if you get older you could force an update of the repo's :

<pre>yum clean all
yum update glibc</pre>

Now if you done this, you should restart all services that are facing network, as that is the attack vector. In short :

<pre>service sshd restart
service httpd restart
service nginx restart
service iptables restart
service postfix restart
...</pre>

**better** :

<pre>reboot -h now</pre>

SystemD has alternative :

<pre>systemctl daemon-reexec</pre>

Need a good &[ deep article ?][6] [debian & friends update info.][7]

Stay updated & stay safe!

 [1]: https://access.redhat.com/articles/1332213
 [2]: http://heartbleed.com/
 [3]: https://en.wikipedia.org/wiki/Shellshock_(software_bug)
 [4]: https://access.redhat.com/security/cve/cve-2015-7547
 [5]: http://dankaminsky.com/2016/02/21/ghost/
 [6]: https://ma.ttias.be/critical-glibc-buffer-overflow-vulnerability-getaddrinfo-linux-cve-2015-7547-cve-2015-5229/
 [7]: http://www.cyberciti.biz/faq/linux-patch-cve-2015-7547-glibc-getaddrinfo-stack-based-buffer-overflow/