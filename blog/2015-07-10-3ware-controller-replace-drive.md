---
title: 3ware tw_cli controller replace drive
author: svennd

date: 2015-07-10T19:48:21+00:00
url: /3ware-controller-replace-drive/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - 3ware
  - raid-controller
  - recovery

---
With different drive sizes (60Gb -> 6Tb), it becomes more difficult to get a cold spares (opposite of [hot spare][1]) for every disk size. Thank god, most (can I say all ?) raid-controllers can handle larger disks then the original member disk sizes.  This means that when a RAID 6 array has one dead disk of 4Tb size one can replace this with anything at least >=4Tb.

Normally disks have a decent warranty for sure, enterprise disks, however the time till replacement can be pretty long, so the way I use it is I take a cold spare pop it in, get the RMA going and when the replacement comes I do swap out the temporary disk with the replacement.  This however leaves RAID header info on the cold drive, so next time you hot-swap the disk, the raid-controller won't touch your disk. Since it believes that the disk is part of another RAID set. Lucky for us we can clean this data. This is how I done it for 3ware.

<!--more-->

First after replacing the disk. Let the controller scan for new disks.

<pre>tw_cli rescan</pre>

Since the disk might be recognized as part of an existing raid unit, delete this first. You might be surprised that suddenly a new incomplete (=broken) raid unit will be there. This is a good indication the disk has been used before.

<pre># the X being the controller number (mostly 0) and Y the unit (mostly all_units+1)
tw_cli maint deleteunit cX uY</pre>

_note : X is the controller, this defaults mostly to 0 but if you have multiple it might be different, check first. Y is the unit itself, be sure to take the right one otherwise you might break your degraded unit._

Now the disk, should no longer be marked incomplete, so we can add it to the degraded unit. Since this disk was cleaned mannually it won't be picked up automatically, so we need to initiate the rebuild manually :

<pre>tw_cli /cX/uY start rebuild disk=Z</pre>

_note : Z is the disk number you wish to replace, on some controllers you need to add a "p" before it, for others this is not needed. Some guides will suggest to add the parameter ignoreECC,  this will ignore common [ECC errors][2]. Generally that is not a good idea/_

Happy rebuilding !

Extra tip : In case you have allot of disks, you can let a disk location blink to show where you want to pull the drive, blink led to identify on tw_cli

<pre>tw_cli /c8/p36 set identify=on</pre>

 [1]: https://en.wikipedia.org/wiki/Hot_spare
 [2]: https://en.wikipedia.org/wiki/Forward_error_correction