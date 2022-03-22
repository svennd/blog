---
title: how to install SNMP service on Centos 8.2
author: svennd

date: 2016-02-21T12:23:34+00:00
url: /how-to-install-snmp-service-on-centos-7-2/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
![librenms, my hero monitor!](/img//2015/07/25449688-1.png)


In this guide I just install configure SNMP daemon on Centos 8.2 (latest at this writing). SNMP is used to read out the state of the machine, this includes CPU, network (-traffic, -settings,...), memory, ... I can suggest [LibreNMS][1], for the monitor. Back to setting SNMP daemon up !<!--more-->

**Install  
** Install the service using our loved yum.

<pre>yum install net-snmp net-snmp-utils</pre>

**Config**  
To the configuration; which can be found here : /etc/snmp/snmpd.conf  
I however remove the original one -most of the time- and just copy/paste the one I use on all servers; Keep the original for reference, although its very verbose (IMHO)

<pre>mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.ori</pre>

This is the config I use and why in the comments :

<pre># this create a  SNMPv1/SNMPv2c community named "my_servers"
# and restricts access to LAN adresses 192.168.0.0/16 (last two 0's are ranges)
rocommunity my_servers 192.168.0.0/16

# setup info
syslocation  "rack 1, room 3, Antwerpen serverrroom"
syscontact  "Svenn"

# open up
agentAddress  udp:161

# run as
agentuser  root

# dont log connection from UDP:
dontLogTCPWrappersConnects yes

# fix for larger then 2TB disks (raid!)
realStorageUnits 0</pre>

<p class="EnlighterJSRAW" data-enlighter-language="null">
  <strong><span style="font-family: Georgia, 'Times New Roman', 'Bitstream Charter', Times, serif;">Network security</span></strong>
</p>

<p data-enlighter-language="null">
  Since this is a network tool, we need to allow incoming connections through the firewall; For Centos 6-7 this is mostly IPTABLES, if you are using Centos 8 then FirewallD is the one to config;
</p>

<p data-enlighter-language="null">
  <em>IPTABLES</em>
</p>

<pre>iptables -A INPUT -s 192.168.0.0/16 -p udp --dport 161 -j ACCEPT</pre>

Be sure to change the ip range!

_FirewallD_

<pre>firewall-cmd --permanent --add-service=snmp
firewall-cmd --reload</pre>

Note I did not add the range here (you could add a zone).

**Starting the ****daemon**

<pre>systemctl start snmpd
systemctl enable snmpd
</pre>

**Reduce verbose**

In older versions, snmpd was really verbose. By default EVERY connection is logged in rsyslog. Considering I poll every 5 minutes, this adds up in /var/log/messages. So lets down it a bit, this can be done in /etc/sysconfig/snmpd add the following, to reduce the verbosity :

OPTIONS="-Ls3d"

**Extending snmp**

Librenms can track multiple snmp extends, most relevant for me are [zfs][2], [apc ups][3], [nfs server][4] (my own creation), these can generally be added using a small executable script adding this in _snmp.conf_

<pre>extend nfs-server /etc/snmp/nfs-server.sh
extend zfs /etc/snmp/zfs-linux
extend ups-apcups /etc/snmp/ups-apcups.sh</pre>

For more info on these, I would refer to the [documentation of LibreNMS][5].

![librenms apps overview](/img/2016/02/overview_librenms-1024x526.png)

**Testing ... 123  
** On the machine you are installing LibreNMS or any other NMS package try :

<pre>snmpwalk -c my_servers -v1 servername SNMPv2-MIB::sysDescr.0</pre>

This should return the system description. (only change -c COMMUNITY and -v1 SERVERNAME/IP)

&nbsp;

Happy monitoring!

_changes :_

  * added the option _dontLogTCPWrappersConnects_ for less verbose setup
  * added the option _realStorageUnits_ for a bug work around on large disks (+32TB)
  * tested for Centos 7.3 (08/03/2017)
  * tested for Centos 7.5, added info on extend option (07/08/2018)
  * On Centos 8, or 7, added instructions for firewalld
  * rewritten the post to focus on Centos 8.

 [1]: http://www.librenms.org
 [2]: https://docs.librenms.org/#Extensions/Applications/#zfs
 [3]: https://docs.librenms.org/#Extensions/Applications/#ups-apcups
 [4]: https://docs.librenms.org/#Extensions/Applications/#nfs-server
 [5]: https://docs.librenms.org/#Extensions/Applications/
