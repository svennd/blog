---
title: setup central SysLog server on Centos 7
author: svennd

date: 2015-11-07T21:35:07+00:00
url: /setup-central-syslog-server-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Today I was searching for tools to centralize the logging of some 20 odd Linux servers, while this is no endpoint in my research, I "logged" the method I used to setup my test/demo servers using good old _rsyslog_.

While there are allot possibilities towards logging, I'd like :

  * local + remote logging
      * It would be nice if I could log into one "remote" machine/service and see the log files of all machines I need to "maintain". However keeping a local log file is also important since not everybody will have access to the central machine logging server/service.
  * KISS
      * As little as possible dependencies, preferably it should run up from a ancient Centos 5 to a bleeding edie nighly Ubuntu virtual machine.  So preferably a default package in the common linux distro's.
  * little overhead
      * The overhead for client servers should be as small as possible, since they do have a real job beside logging.

Most of these points are checked off when working with _rsyslog_, so I took that solution out for a spin. With _rsyslog_ we can filter out some irrelevant messages (like DHCP requests), use different logging servers for different levels/labels or service ... its pretty powerful and best of all, the package is in Centos by default. 🙂

  ![my &quot;work&quot; servers send their log files to the central Rsyslog server, while keeping a local log file also.](/img/2015/07/25449728-1.png)

**Server Setup**

This machine is going to be the central logging server. (_rsyslog_ server)

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># lets be good to our logging server
yum update -y

# install if not yet here
yum install rsyslog rsyslog-doc

# edit
nano +15 /etc/rsyslog.conf</pre>

Replace

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># Provides UDP syslog reception
#$ModLoad imudp
#$UDPServerRun 514

# Provides TCP syslog reception
#$ModLoad imtcp
#$InputTCPServerRun 514</pre>

With  
_note : this only enables UDP logging, <code class="EnlighterJSRAW" data-enlighter-language="null">modload imtcp</code> does TCP. I picked UDP since I don't care for specific order of the log messages, even if a messages get lost now and again that's ok. _

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># Provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514

# Provides TCP syslog reception
#$ModLoad imtcp
#$InputTCPServerRun 514</pre>

Reload the service and check if it is listening.

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># lets load the new config
systemctl restart rsyslog

# or if you are like me and like the good'ol way
service rsyslog start

# now lets check if our server is listening to the port
netstat -anup | grep 514
udp        0      0 0.0.0.0:514             0.0.0.0:*                           27285/rsyslogd      
udp6       0      0 :::514                  :::*                                27285/rsyslogd     

# or if you also enabled tcp
netstat -antup | grep 514
</pre>

Also open ports on the firewall! Example with iptables (restricting on INPUT) :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># don't forget to allow this info in firewall
# example for iptables on UDP
iptables -I INPUT -p udp --dport 514 -j ACCEPT

# for tcp
iptables -I INPUT -p tcp --dport 514 -j ACCEPT</pre>

You're server is ready to start logging more servers, onto the client (server 2) !

<span style="color: #000000;"><b>Client Setup</b></span>

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># again be good sysadmin
yum update -y

# install if not there
yum install rsyslog rsyslog-doc

# edit the config
nano +92 /etc/rsyslog.conf

# add, this would log everything
# possible you would wanne restrict this a bit
# see man rsyslog.conf http://linux.die.net/man/5/rsyslog.conf
*.* @SERVER_IP:514
</pre>

Testing the client setup :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false"># (optional) check connection
# with tcp this could be used
telnet SERVER_IP 514
Trying SERVER_IP ...
Connected to SERVER_IP.
Escape character is '^]'.

# with udp client side (package used : nmap-ncat, tcpdump)
# on server
tcpdump 'port 514'

# on client
nc -u SERVER_IP 514
some message
some more messages

# After this restart the logger
systemctl restart rsyslog
# or &lt;3
service rsyslog restart</pre>

<span style="color: #000000;"><b>Test</b></span>

on server :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">tail -f /var/log/messages</pre>

on client :

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">logger -t sysadmin good job kid</pre>

Thats it, so simple, why did I not find this faster ? Good luck with the logging!

Some more resources :

  * [Rsyslog official website](http://www.rsyslog.com/)
  * [Gentoo support wiki on Rsyslog](https://wiki.gentoo.org/wiki/Rsyslog)
