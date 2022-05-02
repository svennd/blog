---
title: lxc server refused to allocate pty (on proxmox)
author: svennd

date: 2017-02-22T10:06:20+00:00
url: /lxc-server-refused-to-allocate-pty-on-proxmox/
thumbnail: /img/2020/02/nightsky.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox

---
I got this weird error, which -to be fair- I haven't exactly found out why it happens but I found a way around it. If you ever figure out how please let me know.

There are some markers of this issue :

Trying to log in remote, seems to work but ends up with : (the console won't open)

<pre>Server refused to allocate pty</pre>

In the /var/log/secure :

<pre>Feb 22 09:59:22 enterprise sshd[702]: Accepted password for root from some_IP port 54406 ssh2
Feb 22 09:59:22 enterprise sshd[702]: pam_unix(sshd:session): session opened for user root by (uid=0)
Feb 22 09:59:22 enterprise sshd[702]: error: openpty: No such file or directory
Feb 22 09:59:22 enterprise sshd[702]: error: session_pty_req: session 0 alloc failed</pre>

Essential here are the _openpty : no such file_ and _session\_pty\_req : session 0 alloc failed. _

This may or may not be related to this issue but in /var/log/messages allot of errors started popping up :

<pre>Feb 21 14:23:41 enterprise udevd-work[903]: inotify_add_watch(6, /dev/zd16, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4100]: inotify_add_watch(6, /dev/loop5, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4113]: inotify_add_watch(6, /dev/ram12, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4108]: inotify_add_watch(6, /dev/ram1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4095]: inotify_add_watch(6, /dev/loop1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4093]: inotify_add_watch(6, /dev/ram13, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4094]: inotify_add_watch(6, /dev/zd0, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4108]: inotify_add_watch(6, /dev/ram4, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4095]: inotify_add_watch(6, /dev/zd16, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4093]: inotify_add_watch(6, /dev/zd0p1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4094]: inotify_add_watch(6, /dev/zd0p2, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise ata_id[4098]: unable to open '/dev/.tmp-block-8:16'
Feb 21 14:23:41 enterprise ata_id[4087]: unable to open '/dev/.tmp-block-8:80'
Feb 21 14:23:41 enterprise ata_id[4083]: unable to open '/dev/.tmp-block-8:64'
Feb 21 14:23:41 enterprise ata_id[4081]: unable to open '/dev/.tmp-block-8:48'
Feb 21 14:23:41 enterprise ata_id[4114]: unable to open '/dev/.tmp-block-8:32'
Feb 21 14:23:41 enterprise ata_id[4102]: unable to open '/dev/.tmp-block-8:128'
Feb 21 14:23:41 enterprise ata_id[4122]: unable to open '/dev/.tmp-block-8:0'
Feb 21 14:23:41 enterprise ata_id[4089]: unable to open '/dev/.tmp-block-8:112'
Feb 21 14:23:41 enterprise ata_id[4105]: unable to open '/dev/.tmp-block-8:96'
Feb 21 14:23:41 enterprise ata_id[4176]: unable to open '/dev/.tmp-block-11:0'
Feb 21 14:23:41 enterprise ata_id[4177]: unable to open '/dev/.tmp-block-11:0'
Feb 21 14:23:41 enterprise udevd-work[903]: inotify_add_watch(6, /dev/sdd, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4085]: inotify_add_watch(6, /dev/sdh, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4082]: inotify_add_watch(6, /dev/sdf, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4080]: inotify_add_watch(6, /dev/sdd2, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4084]: inotify_add_watch(6, /dev/sdg1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4085]: inotify_add_watch(6, /dev/sde1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4106]: inotify_add_watch(6, /dev/sdb2, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[903]: inotify_add_watch(6, /dev/sdd1, 10) failed: Operation not permitted
Feb 21 14:23:41 enterprise udevd-work[4082]: inotify_add_watch(6, /dev/sdd9, 10) failed: Operation not permitted</pre>

### The solution

Comment in the container /etc/rc.sysinit this line : (around line 155, nano +155 /etc/rc.sysinit)

<pre>/sbin/start_udev</pre>

Getting in the container can be done using <code class="EnlighterJSRAW" data-enlighter-language="null">lxc-attach -n ID</code> after commenting that line reboot the container and it worked again for me.

Best of luck !