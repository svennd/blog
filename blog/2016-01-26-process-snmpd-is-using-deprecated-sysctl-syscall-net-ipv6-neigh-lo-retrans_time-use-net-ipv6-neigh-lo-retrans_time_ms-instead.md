---
title: process ‘snmpd’ is using deprecated sysctl (syscall) net.ipv6.neigh.lo.retrans_time; Use net.ipv6.neigh.lo.retrans_time_ms instead
author: svennd

date: 2016-01-26T08:50:34+00:00
url: /process-snmpd-is-using-deprecated-sysctl-syscall-net-ipv6-neigh-lo-retrans_time-use-net-ipv6-neigh-lo-retrans_time_ms-instead/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
This error occurred on server with yum-cron installed; So I ruled out updates, but I was wrong. An update solved this issue, 'bout 100mb of updates to be exact... A new lesson learned : don't blindly trust on yum-cron!

As to this error I believe it was solved in one of these packages :

<pre>Jan 26 09:23:29 Updated: 1:net-snmp-libs-5.7.2-24.el7.x86_64
Jan 26 09:23:49 Updated: 1:net-snmp-agent-libs-5.7.2-24.el7.x86_64
Jan 26 09:24:35 Updated: 1:net-snmp-5.7.2-24.el7.x86_64
Jan 26 09:24:46 Updated: 1:net-snmp-utils-5.7.2-24.el7.x86_64</pre>

&nbsp;