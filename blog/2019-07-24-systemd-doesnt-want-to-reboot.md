---
title: systemd doesn’t want to reboot
author: svennd

date: 2019-07-24T12:06:29+00:00
url: /systemd-doesnt-want-to-reboot/
thumbnail: /img/2020/05/rainbow.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - systemd

---
Today, I had a new issue, a server blankly refused to reboot/shutdown.

<pre>[root@server ~] reboot -h now
Failed to start reboot.target: Connection timed out
See system logs and 'systemctl status reboot.target' for details.
Failed to open /dev/initctl: No such device or address
Failed to talk to init daemon.
</pre>

I first tried :

<pre>[root@server ~]# systemctl --force reboot
Failed to execute operation: Activation of org.freedesktop.systemd1 timed out</pre>

which failed, so I had to get the big guns : <code class="EnlighterJSRAW" data-enlighter-language="null">systemctl --force --force reboot</code>

<pre>[root@server ~]# systemctl --force --force reboot
Rebooting with argument 'now'.
packet_write_wait: Connection to 10.1.255.253 port 22: Broken pipe</pre>

As you see that restarted the server; weird issue, solved [thanks to Luke][1]

 [1]: https://sudoedit.com/linux-server-wont-reboot/