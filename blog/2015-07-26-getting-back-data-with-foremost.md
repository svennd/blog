---
title: Getting back data with Foremost
author: svennd

date: 2015-07-26T19:59:21+00:00
url: /getting-back-data-with-foremost/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - data recovery

---
So you deleted something from a Linux system ? No shame in that, I done it tons of time, not gone judge you for not having backups. We live in an age where data is really, really big and disks are still rather small. (while this is irony, it is kinda true)

So I had to use data recovery since I managed to get my USB stick broken (RAW, so generally no sane format), neither, Linux (crouton, ChromeOS) nor Windows 7 wanted to read from my USB stick anymore. So what now ?

Before you continue, read this warning : (its _red_ so its important)

<span style="color: #ff0000;">When something unexpected happened (disk broke, no backup, raid6 broke, ....), and your backup is not there, stop when you care about the data. You are not a data recovery expert, neither am I, tell your boss/mom/girlfriend/... and hope (s)he doesn't blame you. Then call a data recovery service. Don't try this if you care for the data. Unless its a CD of <em>Justin Bieber</em>, then just go ahead.</span>

[Foremost](http://foremost.sourceforge.net/" target="_blank), is a console recovery tool that will try to find your files using the known patterns. For example, most files start off (header) with telling what they are, that is useful for the programs trying to read them.


  <img aria-describedby="caption-attachment-1064" loading="lazy" class="wp-image-1064 size-full" src="/img//2015/07/29732080-1.png" width="698" height="30" srcset="/img/2015/07/29732080-1.png 698w, /img/2015/07/29732080-1-300x13.png 300w, /img/2015/07/29732080-1-23x1.png 23w, /img/2015/07/29732080-1-670x30.png 670w" sizes="(max-width: 698px) 100vw, 698px" />


Using this technique _Foremost_ will read the entire disk/partition you select and will report back these files when found. While this in theory sounds rather simple method, disks are fragmented, meaning that one file might be written over a way larger space then it really needs, as the disk was filled up multiple times.

Running this tool, can be done on _images_ as such if your disk is broken, running a _dd_ first or even better _dd_rescue_ and then playing with the image file itself might be better. While I had backup of the most important files anyway, and the stick wasn't broken (I think) I just ran it straight from on the stick.

<pre class="EnlighterJSRAW" data-enlighter-language="shell"># find the correct disk
fdisk -l

# start the foremost run
foremost -i /dev/sdb

# start foremost only with jpeg
foremost -i /dev/sdb -t jpeg</pre>

The result :

<pre>File: /dev/sdd
Start: Mon Jul 27 09:06:19 2015
Length: 58 GB (63216549888 bytes)

Num      Name (bs=512)         Size      File Offset     Comment

0:      00475418.jpg           2 MB       243414016
1:      00482138.jpg          67 KB       246854656
2:      00483418.jpg         108 KB       247510016
3:      00492570.jpg         395 KB       252195840
4:      00495834.jpg         591 KB       253867008
....


Finish: Mon Jul 27 09:53:53 2015

3001 FILES EXTRACTED

jpg:= 2047
gif:= 709
mov:= 34
mp4:= 12
rif:= 24
htm:= 6
zip:= 23
png:= 138
pdf:= 8
------------------------------------------------------------------

Foremost finished at Mon Jul 27 09:53:53 2015</pre>
