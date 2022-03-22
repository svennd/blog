---
title: Get firmware version of IPMI module
author: svennd

date: 2016-02-09T07:00:52+00:00
url: /get-firmware-version-of-ipmi-module/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I wanted to know the power usage of a server, on newer modules this was on the webinterface; No luck on a bit older machine though.  I try'd to get to upgrade the firmware (oooo-aaaaa watch out when doing this, its dangerous) to get this information. Finding the IPMI (Intelligent Platform Management Interface) device and getting the info can be done like this :

<pre>ipmitool bmc info</pre>

The ipmitool is in most repo's, but can be found on [sourceforge][1] (its like github but less cool).  If you are against using ipmitools, you can also use this new thing called dmidecode. (its new to me) This will give you the info on IPMI device.

<pre>dmidecode --type 38</pre>

Info for my device was :

<pre># dmidecode 2.12
SMBIOS 2.6 present.

Handle 0x005E, DMI type 38, 18 bytes
IPMI Device Information
        Interface Type: KCS (Keyboard Control Style)
        Specification Version: 2.0
        I2C Slave Address: 0x10
        NV Storage Device: Not Present
        Base Address: 0x0000000000000CA2 (I/O)
        Register Spacing: Successive Byte Boundaries</pre>

Btw, power usage was not on the newest firmware, I tried calculating back from the UPS, but with 4 $servers and only 2 $servers_known. It was a bit to hard, I just bought a larger extra UPS. (#morepoweralwaysworks)

  <img aria-describedby="caption-attachment-978" loading="lazy" class="wp-image-978 size-full" src="/img//2015/07/25450720-1.png" width="702" height="499" srcset="/img/2015/07/25450720-1.png 702w, /img/2015/07/25450720-1-300x213.png 300w, /img/2015/07/25450720-1-1x1.png 1w" sizes="(max-width: 702px) 100vw, 702px" />


 [1]: http://sourceforge.net/projects/ipmitool/
