---
title: Automating zfs snapshots of proxmox using Sanoid
author: svennd

date: 2016-12-01T15:41:39+00:00
url: /zfs-snapshots-of-proxmox-using-sanoid/
thumbnail: /img/2020/03/zfs.jpg
categories:
  - Linux
  - proxmox
  - SysAdmin
  - ZFS
tags:
  - zfs

---
Slowly [ZFS on Linux][1] is becoming the mainstream file system, however its more then just a file system, its a (software) raid,  it allows for snapshots, compression, deduplication, ... its pretty cool, and I'm in love with it. One of the most important features for me is snapshots, those are dirty cheap in ZFS, so cheap everyone should use them. Cause you never know when you will issue a <code class="EnlighterJSRAW" data-enlighter-language="null">rm -rf /*</code> or a cryptovirus hits you. Fixing those, with ZFS snapshots is allot easier and faster. However in order to be able to use them, you need to make them, and honestly, everyone forgets about making snapshots, as a solution ZFS snapshots is an excellent target for automation. One could build a snapshot system from scratch, but since ZFS is so popular, there must be some systems out there. There are some, [zfs-auto-snapshot][2] ([excellent tutorial][3]) is the most common, as its been around from close to the start of the ZOL (ZFS On Linux) project. While it works great, the configuration is limited and we want to able to configure depending on the (sub)volume. I found [Sanoid][4], and the rest, as they say, is history. Sanoid seems [commercial linked][5], but its GNU open-source. Setting it up is easy, but the documentation is a bit lacking right now. So here goes, how I set it up. (while not specific for proxmox, I use it on all new -ZFS- proxmox installations)

![Our snapshot bro.](/img/2016/12/sanoid.png)

**Dependency  
** Proxmox is Debian based, so apt-get :

<pre>apt-get install libconfig-inifiles-perl git</pre>

In case you would use Centos or the likes, these are the same packages :

<pre>yum install perl-Config-IniFiles git</pre>

**Install  
** Then we can go an install Sanoid. I like to just clone the repository, and work from there, there might be other options of-course.

<pre>cd /opt
git clone https://github.com/jimsalterjrs/sanoid
</pre>

Then create a hard link from the repo to the /usr/sbin (general system-wide root privileged scripts) or /usr/local/bin. (change accordingly downstream)

<pre>ln /opt/sanoid/sanoid /usr/sbin/</pre>

Then we need to create the configuration, sanoid will read from /etc/sanoid/sanoid.conf, so that seems a good place to put them. Also put the sanoid.default.conf there, else sanoid refuses to run.

<pre>mkdir -p /etc/sanoid
cp /opt/sanoid/sanoid.conf /etc/sanoid/sanoid.conf
cp /opt/sanoid/sanoid.defaults.conf /etc/sanoid/sanoid.defaults.conf</pre>

After that you should configure sanoid, before putting it in the cron tasks. I use the following pretty self explanatory config :

<pre>####################
# sanoid.conf file #
####################

[tank/subvol-104-disk-1]
        use_template = production

#############################
# templates below this line #
#############################

[template_production]
        # store hourly snapshots 36h
        hourly = 36

        # store 30 days of daily snaps
        daily = 30

        # store back 6 months of monthly
        monthly = 6

        # store back 3 yearly (remove manually if to large)
        yearly = 3

        # create new snapshots
        autosnap = yes

        # clean old snapshot
        autoprune = yes</pre>

Then the cron :

<pre>nano /etc/crontab
*/5 * * * * root /usr/sbin/sanoid --cron</pre>

**Results  
** And after 5 minutes you will see, snapshots popping up,  this cron runs once every 5 minutes, while once or twice an hours should be sufficient, I did not want to stray to far from the original _readme_ in the git repo. That actually does check the snapshots every minute ... After a while the result will be :

<pre>root@rocky:~# zfs list -t snapshot
NAME                                                USED  AVAIL  REFER  MOUNTPOINT
tank/fry@autosnap_2016-11-23_16:05:30_monthly  		14.0M      -  4.13T  -
tank/fry@autosnap_2016-11-24_23:59:01_daily    		13.4M      -  4.13T  -
tank/fry@autosnap_2016-11-25_23:59:01_daily        		0      -  4.13T  -
tank/fry@autosnap_2016-11-26_23:59:01_daily       		0      -  4.13T  -
tank/fry@autosnap_2016-11-27_23:59:01_daily        		0      -  4.13T  -
tank/fry@autosnap_2016-11-28_23:59:01_daily   		1.38G      -  4.14T  -
tank/fry@autosnap_2016-11-29_23:59:01_daily     	472K      -  4.14T  -
tank/fry@autosnap_2016-11-30_23:59:01_daily        		0      -  4.14T  -
tank/fry@autosnap_2016-12-01_00:00:01_monthly     		0      -  4.14T  -
tank/fry@autosnap_2016-12-01_11:00:01_hourly    	191K      -  4.14T  -
tank/fry@autosnap_2016-12-01_12:00:01_hourly    	191K      -  4.14T  -
tank/fry@autosnap_2016-12-01_13:00:01_hourly   		69.6K      -  4.14T  -
tank/fry@autosnap_2016-12-01_14:00:01_hourly    	38K      -  4.14T  -
tank/fry@autosnap_2016-12-01_15:00:01_hourly    	421K      -  4.14T  -
tank/fry@autosnap_2016-12-01_16:00:01_hourly    	534K      -  4.14T  -
tank/leela@autosnap_2016-11-25_11:09:01_monthly     40.4K      -  4.43G  -
tank/leela@autosnap_2016-11-25_11:09:01_daily       38.2K      -  4.47G  -
tank/leela@autosnap_2016-11-25_23:59:01_daily           0      -   883G  -
tank/leela@autosnap_2016-11-26_23:59:01_daily           0      -   883G  -
tank/leela@autosnap_2016-11-27_23:59:01_daily           0      -   883G  -
tank/leela@autosnap_2016-11-28_23:59:01_daily       1.10M      -   883G  -
tank/leela@autosnap_2016-11-29_23:59:01_daily           0      -   862G  -
tank/leela@autosnap_2016-11-30_23:59:01_daily           0      -   862G  -
tank/leela@autosnap_2016-12-01_00:00:01_monthly         0      -   862G  -
tank/subvol-104-disk-1@init                         432K      -   642M  -
tank/subvol-105-disk-1@init                        25.9M      -   689M  -
tank/subvol-106-disk-1@init                            0      -  1.24G  -</pre>

_note : this result is not from this config, I use no yearly's and a lot less hourly snapshots. On top of that, I generally store the configured  system with @init and put data on a separate volume, which I snapshot automated._

There are allot of other zfs "auto" snapshot systems out there, a short list can be found on [wiki complete][6]; Whatever you choose, be sure to try it first, and be sure you are happy with the features. But most important, use snapshots, they are not backups (as they are on the same physical system) but they are so cheap there are little arguments to not use them for profit. Happy snaps!

 [1]: http://zfsonlinux.org
 [2]: https://github.com/zfsonlinux/zfs-auto-snapshot
 [3]: https://briankoopman.com/zfs-automated-snapshots/
 [4]: https://github.com/jimsalterjrs/sanoid
 [5]: http://www.openoid.net/products/
 [6]: http://wiki.complete.org/ZFSAutoSnapshots
