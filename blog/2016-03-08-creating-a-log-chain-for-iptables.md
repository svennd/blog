---
title: Creating a log chain for iptables
author: svennd

date: 2016-03-08T14:58:00+00:00
url: /creating-a-log-chain-for-iptables/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Iptables are cool and there are plenty of iptables guides and best practices out there. I'm not adding to the endless list of x common/best/cutest iptables topics. I'm just sharing my logging chain. Most people wanne log and drop, I wanted to log & accept. When working with offsite ip addresses you have no clue who is doing what, so storing some connection is good practice I believe. See here my awesome iptables ! Remarks are welcome !

<pre># chain ACCEPT_LOG
iptables -N ACCEPT_LOG
iptables -A ACCEPT_LOG -j LOG --log-level 6 --log-prefix ':accept_log:'
iptables -A ACCEPT_LOG -j ACCEPT

# chain ACCEPT_LIMIT_LOG
iptables -N ACCEPT_LIMIT_LOG
iptables -A ACCEPT_LIMIT_LOG -m limit --limit 2/min -j LOG --log-level 6 --log-prefix ':accept_limit_log:'
iptables -A ACCEPT_LIMIT_LOG -j ACCEPT

# internal
iptables -A INPUT -i lo -j ACCEPT

# this is me, I trust me.
iptables -A INPUT -p tcp -s 123.456.789.0 --dport 22 -j ACCEPT

# allow ping
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

# I don't trust these guys but they need acces
iptables -A INPUT -p tcp -s 666.666.666.666 --dport 80 -j ACCEPT_LOG -m comment --comment "Evil within"

# these guys I trust more
iptables -A INPUT -p tcp -s 123.123.123.123 --dport 80 -j ACCEPT_LIMIT_LOG

# existing connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# defaults
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT</pre>

&nbsp;