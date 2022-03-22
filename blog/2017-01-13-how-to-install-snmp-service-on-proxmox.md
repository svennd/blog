---
title: how to install SNMP service on Proxmox
author: svennd

date: 2017-01-13T13:33:11+00:00
url: /how-to-install-snmp-service-on-proxmox/
thumbnail: /img/2017/01/proxmox_librenms-e1590418472803.png
categories:
  - Linux
  - proxmox
  - SysAdmin
tags:
  - proxmox
  - snmp

---
In this guide I just install configure SNMP daemon on Proxmox. SNMP is used to read out the state of the machine, this includes CPU, network (-traffic, -settings,...), memory, ... I can suggest [LibreNMS][1], for the monitor. Back to setting SNMP daemon up !

**Install the service**

<pre>apt-get install snmpd</pre>

**Config the service**

I like to backup the original configuration (for documentation) :

<pre>mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.ori
</pre>

Then I create the config file : /etc/snmp/snmpd.conf

<pre># this create a  SNMPv1/SNMPv2c community named "my_servers"
# and restricts access to LAN adresses 192.168.0.0/16 (last two 0's are ranges)
rocommunity my_servers 192.168.0.0/16

# setup info
syslocation  "rack 1, room 3, serverrroom"
syscontact  "Svenn"

# open up
agentAddress  udp:161

# run as
agentuser  root

# dont log connection from UDP:
dontLogTCPWrappersConnects yes

# fix for disks larger then 2TB
realStorageUnits 0</pre>

If needed add an exception to the firewall :

<pre>iptables -A INPUT -s 192.168.0.0/16 -p udp --dport 161 -j ACCEPT</pre>

And start the service :

<pre># debian starts services on install
service snmpd restart</pre>

And enable by default :

<pre>update-rc.d snmpd enable</pre>

**Test**

after that its time to test if the connection works from the server that is going to listen to the snmpd :

<pre>snmpwalk -c my_server -v1 servername SNMPv2-MIB::sysDescr.0</pre>

This should return something like :

<pre>SNMPv2-MIB::sysDescr.0 = STRING: Linux  ...</pre>

That's it, happy tracking !

 [1]: http://www.librenms.org