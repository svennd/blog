---
title: Dependency failed for Bareos File Daemon service Centos 7
author: svennd

date: 2019-03-20T15:28:36+00:00
url: /dependency-failed-for-bareos-file-daemon-service-centos-7/
thumbnail: /img/2020/05/aleks-marinkovic-computer-ducks.jpg
categories:
  - SysAdmin
tags:
  - bareos

---
When installing [Bareos client on Centos 7][1], I could not start the service with the following "cryptical error" :

<pre>[root@svennd ~]# systemctl restart bareos-fd
A dependency job for bareos-fd.service failed. See 'journalctl -xe' for details.
[root@svennd ~]# systemctl status bareos-fd
● bareos-fd.service - Bareos File Daemon service
   Loaded: loaded (/usr/lib/systemd/system/bareos-fd.service; enabled; vendor preset: disabled                                                                                                                        )
   Active: inactive (dead)
     Docs: man:bareos-fd(8)
Mar 20 18:06:11 svennd.be systemd[1]: Dependency failed for Bareos File Daemon service.
Mar 20 18:06:11 svennd.be systemd[1]: Job bareos-fd.service/start failed with result 'dependency'.
Mar 20 18:06:29 svennd.be systemd[1]: Dependency failed for Bareos File Daemon service.
Mar 20 18:06:29 svennd.be systemd[1]: Job bareos-fd.service/start failed with result 'dependency'.
</pre>

This means the Bareos server will keep trying and eventually fail to backup this client; The "workaround" is found in [this closed ticket.][2] On the client change :

<pre>nano /usr/lib/systemd/system/bareos-fd.service</pre>

Comment this line : (add #)

<pre>#Requires=nss-lookup.target network.target remote-fs.target time-sync.target</pre>

Reload & restart the service, it should now start normally :

<pre>systemctl daemon-reload
systemctl start bareos-fd</pre>

&nbsp;

 [1]: https://www.svennd.be/adding-a-linux-client-to-bareos/
 [2]: https://bugs.bareos.org/view.php?id=203