---
title: 'DigitalOcean : monitoring disk speed.'
author: svennd

date: 2016-03-13T20:12:47+00:00
url: /digitalocean-monitoring-disk-speed/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Recently I had some problems with DigitalOcean, I do like them and [they fixed the problem][1]... but I'm not trusting them blindly anymore. So I ran a little test to check if the disks are really SSD or just some sort of intelligent cache. While my test is -probably- not good enough, If this test works, I'm confident my scripts will do great as well.

I set up a cron that runs this tiny script every minute :

<pre>date +%H:%M &gt;&gt; disk_speed
(dd if=/dev/zero of=/root/test bs=16k count=10000) &&gt;&gt; disk_speed
rm /root/test</pre>

This ran for two days, on both a 5$ machine and a 20$ machine. I was not expecting much, and it seems I'm right while. Some dips in speed are to be expected, but overal the speed is acceptable for VPS. The 5$ machine on average has a write speed of 349 MB/s for the 20$ machine this was even higher : 401 MB/s.  Both machines go under 100MB/s a few times during the measured period, but no more then 5% of the time so I guess this can be accounted to backup or generally some processes that are heavy IO during this time.

The "script" to extract from disk_speed was :

<pre>cat disk_speed | grep -v "10000+0" | sed 's/^.*\(.\{8\}\)$/\1/' | awk 'NR%2{printf $0 "\t"; next;}1' &gt; disk_speed_download</pre>

If you wanne have a look at the collected data [feel free, here!][2] Good points for DO this time.

 [1]: https://www.svennd.be/linode-digitalocean-and-vultr-comparison/
 [2]: https://docs.google.com/spreadsheets/d/1PkaQGZSfmKkjHusHxeBpzdN7wwAq1AIYa5tqNM0y1GM/edit?usp=sharing