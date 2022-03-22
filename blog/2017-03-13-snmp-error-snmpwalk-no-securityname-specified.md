---
title: 'SNMP error : snmpwalk no securityName specified'
author: svennd

date: 2017-03-13T14:00:20+00:00
url: /snmp-error-snmpwalk-no-securityname-specified/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I hit on this annoying little bugger while setting up _snmpd_ :

<pre>snmpwalk -c servers compute-0-1 SNMPv2-MIB::sysDescr.0  No log handling enabled - turning on stderr logging
snmpwalk: No securityName specified</pre>

Version of _snmp_ :

<pre>snmpd -v

NET-SNMP version:  5.5
Web:               http://www.net-snmp.org/
Email:             net-snmp-coders@lists.sourceforge.net
</pre>

In this particilar case I had to specificaly add the security version (v1,v2c,v3) :

<pre>snmpwalk -v 2c -c servers compute-0-1 SNMPv2-MIB::sysDescr.0</pre>

And it works 🙂