---
title: MegaRaid’s MegaCli tool that is not so mega.
author: svennd

date: 2016-01-18T11:26:32+00:00
url: /megaraids-megacli-tool-that-is-not-so-mega/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I got a server with a _LSI MegaRAID 9271-8i_ raid controller in it, god knows the console application is bad. There is no way one can master this without at least 2 ph.D in applied cryptography or similar. You can download the console application from the [Avago][1] website. Where you need to search for :  MegaCli 5.5 P2, this zip contains MegaCli-8.07.14-1.noarch.rpm. (at time of writing) This in turn contains the application to talk to the controller (/opt/MegaRAID/MegaCli/MegaCli64).

So now that you have the console app; feel free to use these commands at your own risk. (They tend to work for me)

Creating a raid0 on every disk separate (= JBOD implementation, which one should **NOT** use btw, since replacing disks is a [enter bad curse word]) Where **E** stands for Enclosure Device ID, and **S** for Slot number. The **a **is followed by the number of the raid card, in case you would have more of these ... monsters. (Note: one can also use ALL)

<pre>MegaCli64 -CfgLdAdd -r0 [E:S] -a0 -NoLog</pre>

How to get info on the disks you ask ?

<pre>MegaCli64 -pdlist -a0</pre>

Save your sanity, shutdown active alarm

<pre>MegaCli64 -AdpSetProp AlarmSilence -aALL</pre>

Replace a disk : (cause a single command would be to easy)

<pre># lets find where the disk is  (blink)
MegaCli64 -PdLocate -start -physdrv[E:S]  -a0

# stop it again
MegaCli64 -PdLocate -stop -physdrv[E:S] -a0

# now mark it as offline
MegaCli -PDOffline -PhysDrv [E:S] -a0

# no mark it as missing
MegaCli -PDMarkMissing -PhysDrv [E:S] -a0

# now make it ready for ejecting the disk (i'm not kidding)
MegaCli -PdPrpRmv -PhysDrv [E:S] -a0

# now replace the disk</pre>

Might add some other gems later, but for now this is my personal cheat sheet;

Nice reference/tools to work with this evil raid card :

  * [megacli common's][2] procedures
  * [A hidden gem megaclisas-status][3]
  * [The lsi.sh MegaCLI interface script][4]

 [1]: http://www.avagotech.com/support/download-search
 [2]: https://supportforums.cisco.com/document/62901/megacli-common-commands-and-procedures
 [3]: https://github.com/omame/megaclisas-status
 [4]: https://calomel.org/megacli_lsi_commands.html