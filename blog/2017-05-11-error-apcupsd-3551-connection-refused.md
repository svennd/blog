---
title: 'Error contacting apcupsd @ localhost:3551: Connection refused'
author: svennd

date: 2017-05-11T13:55:56+00:00
url: /error-apcupsd-3551-connection-refused/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - proxmox
  - SysAdmin
tags:
  - apcupsd

---
When installing apcupsd on proxmox ([similar to centos][1]) I received this error :

<pre>Error contacting apcupsd @ localhost:3551: Connection refused</pre>

When running _apcaccess. _I got more info on the error using checking the service status :

<pre>root@rocky:~# service apcupsd status
● apcupsd.service - LSB: Starts apcupsd daemon
   Loaded: loaded (/etc/init.d/apcupsd)
   Active: active (exited) since Thu 2017-05-11 15:46:28 CEST; 6s ago
  Process: 11837 ExecStop=/etc/init.d/apcupsd stop (code=exited, status=0/SUCCESS)
  Process: 11841 ExecStart=/etc/init.d/apcupsd start (code=exited, status=0/SUCCESS)

May 11 15:46:28 rocky apcupsd[11841]: Please check your configuration ISCONFIGURED in /etc/default/apcupsd
May 11 15:46:28 rocky systemd[1]: Started LSB: Starts apcupsd daemon.
</pre>

Found it !  In <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/default/apcupsd</code> there is a fail-safe variable _ISCONFIGURED_. This protects you against accidentally shutdowns when you are testing/freshly installing.

Just change the _no_ into a _yes_; resulting in :

<pre># Defaults for apcupsd initscript

# Apcupsd-devel internal configuration
APCACCESS=/sbin/apcaccess
ISCONFIGURED=yes</pre>

And restart the service :

<pre>service apcupsd restart</pre>

Now _apcaccess _works fine 🙂

 [1]: https://www.svennd.be/install-apcupsd-on-centos-7/