---
title: Install APCUPSD on Centos 7
author: svennd

date: 2017-02-09T11:00:23+00:00
url: /install-apcupsd-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
A simple how-to install [Apcupsd][1] on _Centos 7_. ApcUpsD is a Daemon for APC UPS's. A UPS is a big battery to keep the servers running when power fails and eventually shutdown clean when batteries are about to run out of juice.  The install process is pretty straightforward.

<!--more-->

### Install

The install package for _Centos_ is not in default repo's, we need to add the _epel _repository:

<pre>yum install epel-release
yum update -y
yum install apcupsd</pre>

Once installed best to set it up as start service, this is easily forgotten so let's do that first :

<pre>chkconfig apcupsd on</pre>

![master slave ups setup](/img/2017/02/master_slave_ups.png)


### Master

Now configure the settings first in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/apcupsd/apcupsd.conf</code> these are my settings for a master server (server that is physically connected to usb/serial)  :

<pre>## apcupsd.conf v1.1 ##
#svennd

UPSNAME UpsRackOne

UPSCABLE usb
UPSTYPE usb
DEVICE
LOCKFILE /var/lock
SCRIPTDIR /etc/apcupsd
PWRFAILDIR /etc/apcupsd
NOLOGINDIR /etc

# delay in s after power over battery (buffer for hikups)
ONBATTERYDELAY 6
# less then x % battery level = shutdown
BATTERYLEVEL 1
# less then x min power = shutdown
MINUTES 5
# 0 : disable +0 : shutdowntimer on battery (testing)
TIMEOUT 0

# tell the user x s before shutdown - linked to NOLOGON
ANNOY 30
# initial delay after power failure
ANNOYDELAY 60
# NOLOGON  [ disable | timeout | percent | minutes | always ]
NOLOGON disable

# after shutdown does apcups need to keep up ?
KILLDELAY 0

# netconfig
NETSERVER on
NISIP 0.0.0.0
NISPORT 3551
EVENTSFILE /var/log/apcupsd.events
EVENTSFILEMAX 10

UPSCLASS standalone
UPSMODE disable

STATTIME 0
STATFILE /var/log/apcupsd.status
LOGSTATS off
DATATIME 0</pre>

_note: these settings shutdown the server down pretty fast, I prefer to control the shutdown than to keep them up as long as possible._

After this, you can start the service, but preferably you should run through the [testing first][2].

<pre>service apcupsd start</pre>

### Slave

Now setting up a slave server is also possible, this server will use the network to listen to a master server (data connected to ups) for possible power failures. Note that you require to open the 3551 port on the master in order to be able to communicate.

<pre>## apcupsd.conf v1.1 ##
# svennd
UPSNAME ALFA
UPSCABLE ether
UPSTYPE net
DEVICE 123.123.123.123:3551
LOCKFILE /var/lock
# default is 10 - testing
POLLTIME 5

# stuff
SCRIPTDIR /etc/apcupsd
PWRFAILDIR /etc/apcupsd
NOLOGINDIR /etc

# settings
ONBATTERYDELAY 6
BATTERYLEVEL 5
MINUTES 6
TIMEOUT 0
ANNOY 300
ANNOYDELAY 60
NOLOGON disable
KILLDELAY 0

# no netservice on this machine
# netservice needed for service :/
NETSERVER on
NISIP 0.0.0.0
EVENTSFILE /var/log/apcupsd.events
EVENTSFILEMAX 10
UPSCLASS standalone
UPSMODE disable
STATTIME 0
STATFILE /var/log/apcupsd.status
LOGSTATS off
DATATIME 0</pre>

_note: change & test all value's but be sure to change DEVICE to your master server._

### Pitfalls & Remarks

**1. communication failure**

For some reason my UPS's stop communicating now and again, so don't start shutting down the moment they lose communication. This is even a bigger issue on busy networks. Be sure to also put the switch on the UPS.

**2. reading status**

For _Centos 6_ you could use <code class="EnlighterJSRAW" data-enlighter-language="null">service apcupsd status</code> for systemD systems (_Centos 7_) this does not work any longer, and you can use :

<pre>apcaccess status</pre>

**3. custom actions**

You can adapt the actions the daemon will take on certain events. For example on commfailure you can edit : <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/apcupsd/commfailure</code>

**4. Windows crybaby**

Apcupsd is a cross platform tool, you can install it on Windows servers. The network guys however called the apcupsd network activity "suspicious", and wrongly marked the machine as infected.

&nbsp;

Apcupsd has a really nice [online manual][3] if you get stuck somewhere that is a good point to start.

 [1]: http://www.apcupsd.com/
 [2]: http://www.apcupsd.org/manual/manual.html#testing-apcupsd
 [3]: http://www.apcupsd.org/manual/manual.html
