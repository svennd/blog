---
title: End of system activity file unexpected
author: svennd

date: 2017-01-30T19:53:25+00:00
url: /end-of-system-activity-file-unexpected/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I received this weird error a few days ago:

<pre>End of system activity file unexpected</pre>

<!--more-->

By received I mean received in the /var/spool/mail/root mail :

<pre>From root@server  Thu Jan 19 23:53:01 2017
Return-Path: &lt;root@server&gt;
X-Original-To: root
Delivered-To: root@server
Received: by server (Postfix, from userid 0)
        id D352D25133; Thu, 19 Jan 2017 23:53:01 +0100 (CET)
From: root@server (Cron Daemon)
To: root@server
Subject: Cron &lt;root@server&gt; /usr/lib64/sa/sa2 -A
Content-Type: text/plain; charset=UTF-8
Auto-Submitted: auto-generated
X-Cron-Env: &lt;LANG=en_US.UTF-8&gt;
X-Cron-Env: &lt;SHELL=/bin/sh&gt;
X-Cron-Env: &lt;HOME=/root&gt;
X-Cron-Env: &lt;PATH=/usr/bin:/bin&gt;
X-Cron-Env: &lt;LOGNAME=root&gt;
X-Cron-Env: &lt;USER=root&gt;
Message-Id: &lt;20170119225301.D352D25133@server&gt;
Date: Thu, 19 Jan 2017 23:53:01 +0100 (CET)

End of system activity file unexpected</pre>

What does this error means ? I had no clue.  The first thing I checked was a full disk, nothing popped up for me :

<pre>[root@server data]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sdw2             9.5G  8.0G  1.5G  85% /
tmpfs                  16G   68K   16G   1% /dev/shm
/dev/sdw1             241G  206M  228G   1% /boot
/dev/sdw5             1.6T   70M  1.5T   1% /home
/dev/sdx1             109T   98T   11T  90% /data
jbod2                  22T  4.7G   22T   1% /jbod2</pre>

Nothing weird, at first sight. I checked to see what the /var/log/cron was doing during that time (Thu Jan 19 23:53:01 2017) This is /var/log/cron

<pre>Jan 23 23:50:01 localhost CROND[19121]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jan 23 23:53:01 localhost CROND[19274]: (root) CMD (/usr/lib64/sa/sa2 -A)
Jan 24 00:00:01 localhost CROND[19635]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jan 24 00:01:01 localhost CROND[19689]: (root) CMD (run-parts /etc/cron.hourly)
Jan 24 00:01:01 localhost run-parts(/etc/cron.hourly)[19689]: starting 0anacron
Jan 24 00:01:01 localhost anacron[19700]: Anacron started on 2017-01-24
Jan 24 00:01:01 localhost run-parts(/etc/cron.hourly)[19702]: finished 0anacron
Jan 24 00:01:01 localhost run-parts(/etc/cron.hourly)[19689]: starting rsync_cron.sh
Jan 24 00:01:01 localhost anacron[19700]: Jobs will be executed sequentially
Jan 24 00:01:01 localhost anacron[19700]: Normal exit (0 jobs run)
</pre>

So what is sa1/sa2 ? The [man of sa1/2][1] to the rescue :

<pre>sa2 - Write a daily report in the /var/log/sa directory</pre>

Aaha, /root write access; now lets look at the first line in df -h :

<pre>/dev/sdw2             9.5G  8.0G  1.5G  85% /</pre>

1.5 G free is not that much but enough for a simple log file no ?  The reall mystery was solved when I read back on the /var/spool/mail/root :

<pre>From root@server  Thu Jan 19 06:23:15 2017
Return-Path: &lt;root@server&gt;
X-Original-To: root
Delivered-To: root@server
Received: byserver (Postfix, from userid 0)
        id 7692325002; Thu, 19 Jan 2017 06:23:14 +0100 (CET)
From: Anacron &lt;root@server&gt;
To: root@server
Content-Type: text/plain; charset="ANSI_X3.4-1968"
Subject: Anacron job 'cron.daily' on server
Message-Id: &lt;20170119052314.7692325002@server&gt;
Date: Thu, 19 Jan 2017 06:23:14 +0100 (CET)

/etc/cron.daily/mlocate.cron:

/usr/bin/updatedb: I/O error while writing to `/var/lib/mlocate/mlocate.db.E6o5gC': No space left on device</pre>

mlocate is running an update on all the filesystem, which on this particular file server is quit extensive. Disabling the cron, and removing the mlocate database freed enough to keep the machine running without this error.

<pre>rm -rf /var/lib/mlocate/mlocate.db
sudo chmod -x /etc/cron.daily/mlocate.cron</pre>

Note that I don't use _locate_ (_find_ database backed file search), this process will update the database with all the files nightly. So for me removing it and disabling the cron was the best option. In case you use locate, you could soft-link it to a better location. (ln -s)

&nbsp;

fix :

<pre>rm -rf /var/lib/mlocate/mlocate.db

sudo chmod -x /etc/cron.daily/mlocate.cron
</pre>

Additional I dropped the amount of kernels that is kept :

<pre>yum install yum-utils
package-cleanup --oldkernels --count=2</pre>

Then edit /etc/yum.conf

<pre>installonly_limit=3</pre>

note : always good practice to keep a few older kernels

 [1]: https://linux.die.net/man/8/sa2