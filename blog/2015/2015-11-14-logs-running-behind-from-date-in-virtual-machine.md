---
title: logs running behind from date in virtual machine
author: svennd

date: 2015-11-14T21:00:29+00:00
url: /logs-running-behind-from-date-in-virtual-machine/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
On a virtual server,  <code class="EnlighterJSRAW" data-enlighter-language="shell">date</code> was responding with the correct time/date. However logs where filling up with a wrong time. I normally change the timezone like this : (adapt as needed)

<pre class="EnlighterJSRAW" data-enlighter-language="shell">ln -sf /usr/share/zoneinfo/Europe/Brussels /etc/localtime
[root@main ~]# date
Sat Nov 14 21:55:22 CET 2015</pre>

Also in <code class="EnlighterJSRAW" data-enlighter-language="shell">/etc/sysconfig/clock</code> change to : (adapt as needed)

<pre>ZONE="Europe/Brussels"</pre>

Even when those are done the logs kept on showing up logs very long time ago, the cause was rsyslog running behind... never happened before so :

<pre>service rsyslog restart</pre>

This was also causing my fail2ban to be to easy on this spammers I believe! Questions, remarks, just shoot below !

&nbsp;

Update : seems /etc/sysconfig/clock is not per definition the problem, its rsyslog that is keeping the old date, just restarting ryslog seems to be enough!