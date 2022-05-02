---
title: resize usb stick broken due to dd
author: svennd

date: 2015-08-31T12:56:03+00:00
url: /resize-usb-stick-broken-due-to-dd/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
My USB device (8Gb), I used to install a Linux distro on, (as a platform to start installation on a server). Was kinda messed up... Instead of giving 8Gb, it was showing ~300mb and refusing to format in Windows. Even in linux parted and fdisk did not show the right size, combined with allot of errors.

I was able to solve this re-sizing using [BOOTICE][1], I found how [on this page. ][2] Notice the Linux method there was not working in my case.

Hope this helps some people desperately searching for a solution.

 [1]: http://www.majorgeeks.com/files/details/bootice.html
 [2]: http://www.pendrivelinux.com/restoring-your-usb-key-partition/