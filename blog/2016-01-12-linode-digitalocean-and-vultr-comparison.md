---
title: Linode, DigitalOcean and VULTR Comparison
author: svennd

date: 2016-01-12T09:32:13+00:00
url: /linode-digitalocean-and-vultr-comparison/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - review

---
Source: _[Linode, DigitalOcean and VULTR Comparison][1]_

I'm currently having allot of problems with DigitalOcean, I don't know if my setup/configuration is bad or DO is just crapping on my VPS...

<pre>time dd if=/dev/zero of=test bs=16k count=10000
10000+0 records in
10000+0 records out
163840000 bytes (164 MB) copied, 12.0611 s, 13.6 MB/s

real 0m12.093s
user 0m0.004s
sys 0m0.374s</pre>

This is not really giving allot of hope ...

// update

I contacted the support and they offered -after confirmation of these results- to move to another hypervisor in the same region. Weirdly enough they asked me to shutdown the machine myself, then respond to the ticket. While I know I could have floating ip/failover/... they could have just shutdown, move and restart. Minimizing the downtime was not an option. That said, they where quick to help me out, see the timeline :

  * me> Created ticket @ 10:24
  * **digitalocean> **First response @ 10:58
  * My repeat test @ 11:25
  * **digitalocean> **second response @ 11:54
  * confirmation of move @ 12:08
  * **digitalocean>** move confirmed. @ 12:43

I had database backed up, and took a snapshot before confirming the move;  Cause I had to shutdown myself the downtime was ~2 hours. The sad part is, the upgrade itself only took 4 min 2 seconds and 8 seconds to boot up again. So a 5 minute "upgrade" took the site out for 2 hours.

Now rerunning this dd the lowest value was 177MB/s and the highest 646 MB/s.

<pre>time dd if=/dev/zero of=test bs=16k count=10000
10000+0 records in
10000+0 records out
163840000 bytes (164 MB) copied, 0.253633 s, 646 MB/s

real    0m0.256s
user    0m0.007s
sys     0m0.247s
</pre>

That is more like a RAID5 SSD speed.

 [1]: http://blog.due.io/2014/linode-digitalocean-and-vultr-comparison/