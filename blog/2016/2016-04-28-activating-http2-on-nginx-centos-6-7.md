---
title: Activating http/2 on Nginx, Centos 6.7
author: svennd

date: 2016-04-28T08:08:25+00:00
url: /activating-http2-on-nginx-centos-6-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
It was brought to my attention that [Nginx][1] finally by-default supports http/2, no longer compiling from source or going dev./beta channels ! Although the most fancy items of http/2 like server side pushes are not included yet, still worth the upgrade.

This is how I activated http/2 on my vps.

First I checked if it was needed to upgrade as I generally have a [yum-cron][2] running.

<pre># cat /var/log/yum.log
Apr 16 04:11:30 Updated: selinux-policy-3.7.19-279.el6_7.9.noarch
Apr 16 04:12:18 Updated: selinux-policy-targeted-3.7.19-279.el6_7.9.noarch
Apr 20 03:29:37 Updated: nginx-1.9.15-1.el6.ngx.i386
Apr 23 03:33:24 Updated: tzdata-2016d-1.el6.noarch
</pre>

Last version was released yesterday : 2016-04-26 nginx-1.10.0, so not yet, lets go ahead and update !

<pre>yum update nginx
Loaded plugins: etckeeper, fastestmirror
Setting up Update Process
Loading mirror speeds from cached hostfile
 * base: mirror.nforce.com
 * epel: ftp.nluug.nl
 * extras: mirror.prolocation.net
 * updates: ftp.nluug.nl
 * webtatic: uk.repo.webtatic.com
No Packages marked for Update</pre>

Wuuut ! +1 day after release and still not in the repo's, a well, lets just go ahead and download it straight from nginx.  If you wait a few days this Nginx 1.10.0.1 should be in the repo's, for now I just went ahead and downloaded & installed it.

<pre>wget http://nginx.org/packages/centos/6/i386/RPMS/nginx-1.10.0-1.el6.ngx.i386.rpm

# yum update nginx-1.10.0-1.el6.ngx.i386.rpm
Loaded plugins: etckeeper, fastestmirror
Setting up Update Process
Examining nginx-1.10.0-1.el6.ngx.i386.rpm: nginx-1.10.0-1.el6.ngx.i386
Marking nginx-1.10.0-1.el6.ngx.i386.rpm as an update to nginx-1.9.15-1.el6.ngx.i386
Loading mirror speeds from cached hostfile
 * base: mirror.nforce.com
 * epel: ftp.nluug.nl
 * extras: mirror.prolocation.net
 * updates: ftp.nluug.nl
 * webtatic: uk.repo.webtatic.com
Resolving Dependencies
--&gt; Running transaction check
---&gt; Package nginx.i386 0:1.9.15-1.el6.ngx will be updated
---&gt; Package nginx.i386 0:1.10.0-1.el6.ngx will be an update
--&gt; Finished Dependency Resolution

Dependencies Resolved

=================================================================================================
 Package Arch Version Repository Size
=================================================================================================
Updating:
 nginx i386 1.10.0-1.el6.ngx /nginx-1.10.0-1.el6.ngx.i386 2.0 M

Transaction Summary
=================================================================================================
Upgrade 1 Package(s)

Total size: 2.0 M
Is this ok [y/N]: y
Downloading Packages:
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
etckeeper: pre transaction commit
 Updating : nginx-1.10.0-1.el6.ngx.i386 1/2
 Cleanup : nginx-1.9.15-1.el6.ngx.i386 2/2
etckeeper: post transaction commit
 Verifying : nginx-1.10.0-1.el6.ngx.i386 1/2
 Verifying : nginx-1.9.15-1.el6.ngx.i386 2/2

Updated:
 nginx.i386 0:1.10.0-1.el6.ngx

Complete!</pre>

Tada :

<pre># nginx -v
nginx version: nginx/1.10.0</pre>

Now to activate http/2 :

change

<pre>listen 443 ssl;</pre>

to

<pre>listen 443 ssl http2;</pre>

Under the server {}, I restarted the server, but a reload might be enough, also test before applying.

<pre># nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
# service nginx restart
Stopping nginx:                                            [  OK  ]
Starting nginx:                                            [  OK  ]
</pre>

As to the speed difference, I tried on a single page and did not see any difference, this is not where http/2 will shine on :

HTTP1.1

<img loading="lazy" class="alignnone wp-image-995 size-full" src="/img//2015/07/25452704-1.png" width="537" height="32" srcset="/img/2015/07/25452704-1.png 537w, /img/2015/07/25452704-1-300x18.png 300w, /img/2015/07/25452704-1-17x1.png 17w" sizes="(max-width: 537px) 100vw, 537px" /> 

HTTP/2

<img loading="lazy" class="alignnone wp-image-996 size-full" src="/img//2015/07/25452784-1.png" width="538" height="32" srcset="/img/2015/07/25452784-1.png 538w, /img/2015/07/25452784-1-300x18.png 300w, /img/2015/07/25452784-1-17x1.png 17w" sizes="(max-width: 538px) 100vw, 538px" /> 

I cherry picked the better result on http/2 for people not reading the page 🙂 (I'm assuming some sort of cache was in play)

 [1]: http://nginx.org/
 [2]: http://linux.die.net/man/8/yum-cron