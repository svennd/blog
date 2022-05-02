---
title: SNMP OID to Text
author: svennd

date: 2017-05-04T13:14:26+00:00
url: /snmp-oid-to-text/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
tags:
  - snmp

---
I have been searching high and low for this, and it's not even the first time. Since my brain did not store this long term; I'm sharing it here.

When extending snmpd like this : (in /etc/snmp/snmpd.conf)

<pre>extend zfs-arcstat /usr/bin/cat /proc/spl/kstat/zfs/arcstats</pre>

The output can be queried using :

<pre>NET-SNMP-EXTEND-MIB::nsExtendOutputFull."zfs-arcstat"</pre>

"zfs-arcstat" is a free to choose name. My search, is how can you represent this free name in a single numeric OID;  This can be done using snmptranslate, with the flags **-0n**.

Take note that one has to escape the quotes.

<pre># snmptranslate -On NET-SNMP-EXTEND-MIB::nsExtendOutputFull.\"zfs-arcstat\"
.1.3.6.1.4.1.8072.1.3.2.3.1.2.11.122.102.115.45.97.114.99.115.116.97.116</pre>

The opposite is also possible, without any flags :

<pre># snmptranslate .1.3.6.1.2.1.1.3.0
SNMPv2-MIB::sysUpTime.0</pre>

It was in the [snmp docs][1] all along.

Well perhaps writing this post will help me remember;

 [1]: http://net-snmp.sourceforge.net/tutorial/tutorial-5/commands/snmptranslate.html