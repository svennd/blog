---
title: Automating snapshots with pyznap on Centos 7
author: svennd

date: 2019-02-22T12:00:11+00:00
url: /automating-snapshots-with-pyznap-on-centos-7/
thumbnail: /img/2020/05/md-mahdi-lHMatucgfVk-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - pyznap
  - zfs

---
So I was happily using [sanoid][1], when someone made me aware of [pyznap][2] (thanks !). Ever since that comment, it was on my to-do list to check out _pyznap_, but if a system works, why change it ? Definitely a important cog such as automated snapshots.

The new version of [sanoid][3], breaks comparability with older versions (at least at config level) and is not documented well at the moment; One has to look into the [pull requests][4] to actually understand what is required to get it running. I know open-source project are sometimes in large changes, and its all run on love & joy, but its a sorry state for a project of ~39 contributors. I also find it useful if a project or tool is simple in its setup and understanding for people not looking at this daily.  I might sound critically, but I still think _sanoid_ is a wonderful tool, but personally I just need to get it up & running in 10 minutes and then move on. The feature list of _sanoid_ and its companion _syncoid_ seem ever growing and with it the complexity to find out what is going wrong, that it was time to take _pyznap_ for a run.

And _pyznap_ is actually a shining gem, I got it up & running in 10 minutes. Nothing fancy, just works out of the box. So here's how I set it up;

First I needed to install [Python 3.5+][5] version on Centos 7, I won't go into detail, cause its basically part of every fresh install these days.

<pre>yum install yum-utils groupinstall development
yum install https://centos7.iuscommunity.org/ius-release.rpm
yum install python36u python36u-pip</pre>

After that, install _pyznap _using pip; we need to specify the version otherwise it will take 2.7 (still default on Centos 7)

<pre>pip3.6 install pyznap</pre>

This installs all dependency's, optional you can install [_pv_][6] and [_mbuffer_][7] to visualize transfer speed on sending snapshots to backup locations. lzop can be used to compress the stream

<pre>yum install pv mbuffer lzop</pre>

Now onto the setup/configuration; This tool wil generate a default config and directory if you set it up :

<pre>[root@overflow ~]# pyznap setup -p /etc/pyznap
Feb 21 16:06:30 INFO: Starting pyznap...
Feb 21 16:06:30 INFO: Initial setup...
Feb 21 16:06:30 INFO: Creating directory /etc/pyznap...
Feb 21 16:06:30 INFO: Creating sample config /etc/pyznap/pyznap.conf...
Feb 21 16:06:30 INFO: Finished successfully...</pre>

After the setup; its time for configuration, all items are clearly documented. One remark : don't put # (hashtag) behind to comment; this will generate errors as only lines started with # (hashtag) are ignored;

<pre># default settings parent
[data]
  # every $cron runtime (~15 minutes)
  frequent = 2
  hourly = 6
  daily = 3
  weekly = 1
  monthly = 0
  yearly = 0
  # take snapshots ?
  snap = no
  # clean snapshots ?
  clean = no
  
[data/brick1]
  snap = yes
  clean = yes
  dest = ssh:22:root@backup:backup/brick1
  dest_key = /root/.ssh/id_rsa_backup
  
[data/brick2]
  daily = 24
  snap = yes
  clean = yes</pre>

To give some information I have one pool split up in multiple sub-file systems. (data is the parent and data/brick* are the actually location for data) This means that I can setup defaults for the data but don't really want snapshots of that, as no data resides there; Only in the bricks, so I overwrite the defaults (snap/clean);

A special case is <code class="EnlighterJSRAW" data-enlighter-language="null">dest =</code> that's a build-in backup system; just make a [password-less ssh login][8] for server and you can leverage this feature; One thing to remark is that the backup server needs to be a ZFS filesystem and not the actually physical location. (a bit of trickery cause the location is /backup/brick1 but one needs to remove the first / for ZFS) if you use this feature, perhaps its worth downgrading a package _cryptography_ cause the latest version generates a insane amount of warnings about upcoming deprecation of some function calls. See the [issue][9].

<pre>pip3.6 install cryptography==2.4.2</pre>

A way to clean up the backup location is also provided by [pyznap][10], one can setup a remote cleanup job for the brick by adding :

<pre># cleanup on backup
[ssh:22:root@backup:backup/brick1]
        frequent = 2
        hourly = 6
        daily = 3
        weekly = 1
        key = /root/.ssh/id_rsa
        clean = yes
</pre>

The only thing to do now is to either make a cron, which is the easiest :

<pre>*/15 * * * *   root    /usr/bin/pyznap snap | logger
0 * * * *   root    /usr/bin/pyznap send | logger</pre>

Take snapshots every 15 minutes (frequent) and sync to the backup location (dest=) once per hour; Other snapshots are taken based on the need; (note | logger sends output to /var/log/messages, by being processed by rsyslog, you could also log to a static file, such as explained by the docs)

Alternative on systemd systems like Centos 7, you can leverage [systemd timers][11]. Instead of cron, but who wants to go into that mess ?

And that's it, automated snapshots <3. Thanks [yboetz][2] for an small but useful tool 🙂

 [1]: https://www.svennd.be/zfs-snapshots-of-proxmox-using-sanoid/
 [2]: https://github.com/yboetz/pyznap
 [3]: https://github.com/jimsalterjrs/sanoid/releases/tag/v2.0.1
 [4]: https://github.com/jimsalterjrs/sanoid/pull/206
 [5]: https://www.svennd.be/install-python-3-6-on-centos-7/
 [6]: https://linux.die.net/man/1/pv
 [7]: https://www.systutorials.com/docs/linux/man/1-mbuffer/
 [8]: https://www.svennd.be/passwordless-ssh-login/
 [9]: https://github.com/yboetz/pyznap/issues/22
 [10]: https://github.com/yboetz/pyznap/issues/25
 [11]: https://wiki.archlinux.org/index.php/Systemd/Timers