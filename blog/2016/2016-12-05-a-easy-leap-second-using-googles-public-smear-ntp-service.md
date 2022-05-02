---
title: A easy leap second, using Google’s public smear NTP service
author: svennd

date: 2016-12-05T13:14:01+00:00
url: /a-easy-leap-second-using-googles-public-smear-ntp-service/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Isn't it nice, not to waste **time,** on an issue you did not know you had until someone pointed out a solution ? Well that's exactly what happened today. Seems on December 31, 2016 (its soon and its the worst possible time if you ask me) there is a [leap second][1]. Since our Earth has some unknown variables changing the rotation speed the time has to be adapted now and again. (quit at random it seems) Leap seconds cause all sorts of fun [problems][2] so unless you want to spend December 31 - January 1 behind a computer screen, better apply this leap-second cream to those chunky machines.

Engineer's at Google came with a nice solution, some hours before or/and after they make the seconds the tiniest bit longer. The coolest part: you can use there expertise, when using there servers for ntp (network time protocol).  Here's how :

**Install** it if you did not use ntp before:

<pre>yum install ntp</pre>

At about line 20, /etc/ntp you should find :

<pre># Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst</pre>

Comment, those 4 lines and add google's

<pre>server time1.google.com iburst
server time2.google.com iburst
server time3.google.com iburst
server time4.google.com iburst</pre>

resulting in :

<pre># Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
#server 0.centos.pool.ntp.org iburst
#server 1.centos.pool.ntp.org iburst
#server 2.centos.pool.ntp.org iburst
#server 3.centos.pool.ntp.org iburst
server time1.google.com iburst
server time2.google.com iburst
server time3.google.com iburst
server time4.google.com iburst</pre>

Restart the ntp service (or start)

<pre>service ntpd restart</pre>

Then validate if the servers have been added correctly. Using _ntpq -p_ note that the results may vary.  As long as its time[1-4].google.com

<pre>ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*time1.google.co 71.79.79.71      2 u   36   64    3   95.522   -0.522   0.190
+time2.google.co 71.79.79.71      2 u   36   64    3  106.206   -0.371   0.052
+time3.google.co 71.79.79.71      2 u   36   64    3    4.824   -0.167   0.034
 time4.google.co 71.79.79.71      2 u   36   64    3  256.651    0.917   0.049
</pre>

hopefully this will ensure a happy new-year not a pager-duty new-year, we all know how that story ended :

![](https://morbotron.com/gif/S01E01/114163/115831.gif?b64lines=)

 [1]: https://en.wikipedia.org/wiki/Leap_second#Slowing_rotation_of_the_Earth
 [2]: https://www.reddit.com/r/AskReddit/comments/vv3uq/reddit_went_down_because_of_a_leap_second_today/