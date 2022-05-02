---
title: Set IPMI to use DHCP
author: svennd

date: 2019-12-04T13:23:20+00:00
url: /set-ipmi-to-use-dhcp/
thumbnail: /img/2020/05/colors.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - ipmi

---
If you installed [ipmitools][1], you can change the IP address source from static to DHCP or otherwise.

To change to DHCP :

<pre>ipmitool lan set 1 ipsrc dhcp</pre>

Check what is now setup :

<pre>ipmitool lan print</pre>

example :

<pre>[root@server~]# ipmitool lan print
Set in Progress         : Set Complete
Auth Type Support       : NONE MD2 MD5 PASSWORD
Auth Type Enable        : Callback : MD2 MD5 PASSWORD
                        : User     : MD2 MD5 PASSWORD
                        : Operator : MD2 MD5 PASSWORD
                        : Admin    : MD2 MD5 PASSWORD
                        : OEM      : MD2 MD5 PASSWORD
IP Address Source       : DHCP Address
IP Address              : -ethernet-
Subnet Mask             : 255.255.0.0
MAC Address             : -mac-
SNMP Community String   : public
IP Header               : TTL=0x00 Flags=0x00 Precedence=0x00 TOS=0x00
BMC ARP Control         : ARP Responses Enabled, Gratuitous ARP Disabled
Default Gateway IP      : -gateway-
Default Gateway MAC     : 00:00:00:00:00:00
Backup Gateway IP       : 0.0.0.0
Backup Gateway MAC      : 00:00:00:00:00:00
802.1q VLAN ID          : Disabled
802.1q VLAN Priority    : 0
RMCP+ Cipher Suites     : 1,2,3,6,7,8,11,12
Cipher Suite Priv Max   : XaaaXXaaaXXaaXX
                        :     X=Cipher Suite Unused
                        :     c=CALLBACK
                        :     u=USER
                        :     o=OPERATOR
                        :     a=ADMIN
                        :     O=OEM
Bad Password Threshold  : 0
Invalid password disable: no
Attempt Count Reset Int.: 0
User Lockout Interval   : 0
</pre>

&nbsp;

Thanks to this [IBM documentation][2].

&nbsp;

Full syntax :

<pre>ipmitool lan set 1 ipsrc [ static | dhcp ] 
ipmitool lan set 1 ipaddr {YOUR DESIRED IP}
ipmitool lan set 1 netmask {YOUR NETMASK}
ipmitool lan set 1 defgw ipaddr 10.0.1.1
</pre>

&nbsp;

 [1]: https://www.svennd.be/install-supermicros-ipmi/
 [2]: https://www.ibm.com/support/knowledgecenter/en/linuxonibm/liabw/liabwresetDHCP.html