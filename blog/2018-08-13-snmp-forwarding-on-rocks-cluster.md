---
title: SNMP forwarding on Rocks Cluster
author: svennd

date: 2018-08-13T12:26:05+00:00
url: /snmp-forwarding-on-rocks-cluster/
thumbnail: /img/2020/05/jonny-caspari-kplYftwYOCY-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - rocks
  - snmp

---
[Rocks][1] distro is a cluster system. It comes with SNMP configured out of the box. It is polled using [Ganglia][2]. Which is working nicely, but I like to have all SNMP data in my favorite SNMP system, [_LibreNMS_][3]. Changing the SNMP configuration to be able to poll from _LibreNMS_ should be a rather straight forward process, however those nodes have no connection to the public network. They have a private VLAN to talk to the head-node and a private VLAN to communicate with the storage array. So to get the SNMP data to Librenms we will have to get crafty with _Iptables_ to get this data to _LibreNMS_ on the public net.

![forward snmp from 161 to 3161](/img/2018/08/images.png) 

###### Configuration

First let's check the _/etc/snmp/snmpd.conf _file from the _Rocks _installation :

<pre>com2sec        notConfigUser   default         public
group  notConfigGroup  v1              notConfigUser
group  notConfigGroup  v2c             notConfigUser
view    all             included        .1             80

access  notConfigGroup  "" any noauth exact all all all</pre>

This config is a bit complex and I figure I won't go back, so I commented it. I decided not to remove it completely, since I don't want to break the possibility to go back to _Ganglia_ should it be an important system in _Rocks_. (_note : it's not)_ I added :

<pre># this create a  SNMPv1/SNMPv2c community named "my_servers"
# and restricts access to LAN adresses 192.168.0.0/16 (last two 0's are ranges)
rocommunity my_servers &lt;our_public_net&gt;/16
rocommunity my_servers 10.1.0.0/16

# setup info
syscontact  "svennD"

# open up
agentAddress  udp:161

# run as
agentuser  root

# dont log connection from UDP:
dontLogTCPWrappersConnects yes</pre>

Important here is, that I added two IP ranges, I'm not sure if the private VLAN (10.1.0.0/16) is even required, but since traffic is going over those devices I just added it.

Next thing is setting up the Iptables on the head-node. Since _Rocks _is already pretty protective (good !) I had to add an extra rule to even allow SNMP polling from the device :

<pre>-A INPUT -p udp -m udp --dport 161 -j ACCEPT</pre>

_Allow the head-node to be polled by LibreNMS by accepting incoming UDP packets over port 161._

To receive packets send from the node on port 161 to the head-node, but forward this to port 3161 externally to _LibreNMS_ (circumventing most known ports and the REJECT rule in _Rocks_ for port 1-1023.) can be done with _prerouting rule_ :

<pre>-A PREROUTING -i eth1 -p udp -m udp --dport 3161 -j DNAT --to-destination 10.1.255.244:161
-A POSTROUTING -o eth1 -j MASQUERADE</pre>

_Note : 10.1.255.244 is the private IP of the node._

So from now on packets should come in, this can be checked using _tcpdump,_ which came in handy during the debugging of this project : _(on the node)_

<pre>tcpdump 'port 161'</pre>

To be able to let _snmpd _answer we needed the information to be forwarded on the head-node, this can be done with a _forward_ rule :

<pre>-A FORWARD -d 10.1.255.244/32 -p udp -m udp --dport 161 -m state --state NEW,RELATED,ESTABLISHED -j</pre>

_note : again 10.1.255.244 is the ip of the node._

Surprisingly the node was unable to answer to the incoming requests. This was due to the fact that, the default route (_route -n) _was pointing towards one of the storage servers. To add a default gateway we can add it using _route_ :

<pre>route add default gw 10.1.1.1 eth0</pre>

_note : 10.1.1.1 is the private VLAN ip of the head-node._

###### Conclusion

Bam! _LibreNMS_ can talk to the node using the public IP of the head-node on port 3161 and to the head-node on port 161. One issue that remains unsolved is on reboot, this setup is lost. _Rocks_ by default will reinstall nodes that reboot. This can be resolved by adapting the configurations on _Rocks _and rebuild the distribution _(rocks create distro)._ However this is rather advanced and (IMHO) difficult to debug. So I did not use that system for this project. Another problem is that its rather work-intense to add all the configuration to all the nodes. (this is only for a single node) This can be resolved most easily using scripts and using _rocks run host_ to execute bits on all the nodes. I decided that I only want one node to be polled as a sample. I already track [opengridscheduler][4] using an extend on the head-node. So this is mostly for debugging. Good luck !

 [1]: http://www.rocksclusters.org
 [2]: http://ganglia.info
 [3]: https://librenms.org
 [4]: https://docs.librenms.org/#Extensions/Applications/#opengridscheduler
