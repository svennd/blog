---
title: Remove iptables PREROUTING NAT rule
author: svennd

date: 2017-03-14T20:00:05+00:00
url: /remove-iptables-prerouting-nat-rule/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I'm still a fan of iptables, I know _firewalld _is most likely the "wrapped iptables" future, but let's hold on to iptables for just a bit longer shall we ? This is a short how-to on cleaning up PREROUTING NAT rules. PREROUTING can't be flushed using <code class="EnlighterJSRAW" data-enlighter-language="null">iptables -F</code> so its a bit different.  
<!--more-->

I got in this situation trying to add tcp/udp prerouting to a machine, that had to forward packets from one side of the network to a other subnet ... well anyway, my iptables contained multiple rules I wanted to get out. So lets go !

First you need to find out what line it is :

<pre>iptables -t nat -L --line-numbers</pre>

The horrible result is :

<pre>iptables -t nat -L --line-numbers
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source               destination         
1    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
2    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
3    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
4    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
5    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
6    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 
7    DNAT       udp  --  anywhere             anywhere            udp dpt:snmptrap to:10.1.255.245:161 

Chain POSTROUTING (policy ACCEPT)
num  target     prot opt source               destination         
1    MASQUERADE  all  --  anywhere             anywhere            

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination</pre>

So now how do I remove a specific rule ? Well by specifying the _table (-t)_, and then _delete (-D) _followed by the chain _(prerouting)_ and the _rulenumber (7)_

<pre>iptables -t nat -D PREROUTING 7</pre>

And that's it, no magic _firewalld_ commands needed !<footer></footer>