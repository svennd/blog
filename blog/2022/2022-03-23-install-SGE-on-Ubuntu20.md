---
author: "Svenn D'Hert"
title: "SGE on Ubuntu 20.04 LTS"
description: "installing SGE (Sun Grid Engine) on Ubuntu 20.04 LTS"
date: 2022-03-23
thumbnail: /img/2022/03/ubuntu_sge.jpg
url: /SGE_on_Ubuntu_20.04_LTS/
---

Setting up SGE on Ubuntu 20.04 LTS, should be easy right? Well, thanks to the internet it was do-able. Not a fun experience at all. Rate 0/5 :star: !

Before I complain too much, there are some very valuable resources available online; The one I relied on most is from [peteris.rocks, sun grid engine installation](https://peteris.rocks/blog/sun-grid-engine-installation-on-ubuntu-server/) before anything you should read that document.

After that perhaps continue here for some more debugging / troubleshooting :wink:!

## Installing issues
Install all required packages, the setup will ask a few items (if you didn't follow peteris.rocks guide to install headless). The values I used :
- local mail only,
- cluster name (free to pick),
- default cell : default (change only if you know why),
- master hostname : (a dns resolvable hostname)
```
apt-get install gridengine-master gridengine-client gridengine-exec gridengine-qmon cpp
```

The first issue was at step one, installing gridengine-master package will actually not install on a clean installation. This is a [known bug since](https://bugs.launchpad.net/ubuntu/+source/gridengine/+bug/1774302) 2018, sadly unresolved.

This error can be in the output :
```
dpkg: error processing package gridengine-master (--configure):
installed gridengine-master package post-installation script subprocess returned error exit status 139
[...]
Errors were encountered while processing:
 gridengine-master
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

However near the end of this bug report, you can see that a single "bad" file is the cause of it, after some of my own debugging. (a few hours) I figured out what was breaking the setup. Lucky for me [cloudsatoz](https://www.cloudsatoz.com/2021/04/19/gridengine-installation-simple-configure/) described the method in which they could work around this bug. His method works but I don't think its needed (anylonger?) to manually compile _jemalloc_.


We can find the error, if we rerun the init_cluster script :
```
sudo -u sgeadmin /usr/share/gridengine/scripts/init_cluster /var/lib/gridengine default /var/spool/gridengine/spooldb sgeadmin
```

The ultimate cause is in `spooldefaults.bin`; So we need to replace that one.
That can be done by downloading an older package and replacing the file.

```
wget http://ftp.debian.org/debian/pool/main/g/gridengine/gridengine-client_8.1.9+dfsg-9_amd64.deb
dpkg -x gridengine-client_8.1.9+dfsg-9_amd64.deb a
cd a/usr/lib/gridengine/
cp spooldefaults.bin /usr/lib/gridengine/
cp libspool*.so /usr/lib/gridengine/
systemctl restart gridengine-master
```

The cloudsatoz method goes and runs ./install_qmaster from `/var/lib/gridengine`. This requires a extra binary : qmake. I'm not sure if this is required or not, but I installed it and linked the current qmake binary in the folder of gridengine.

```
apt-get install qt5-qmake

ln -s /usr/bin/qmake /usr/lib/gridengine/qmake

cd /var/lib/gridengine
./install_qmaster
```

## qhost
After the master installation, I couldn't connect/query on the master.

```
error: commlib error: access denied (client IP resolved to host name "localhost". This is not identical to clients host name "master")
error: unable to send message to qmaster using port 6444 on host "master": got send error
```
Fix by removing from `/etc/hosts` to :

```
127.0.1.1 server  # <-- remove
```

After this, I pretty much could follow [the guide](https://peteris.rocks/blog/sun-grid-engine-installation-on-ubuntu-server/).


## AD users connected
After installing the cluster and making it a submit host; I connected via samba to our AD environment. After logging in to one of the accounts over AD I could submit jobs, but they would directly fail. Upon closer inspection this came out :
```
svennd@server:~$ qstat
job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID
-----------------------------------------------------------------------------------------------------------------
     37 0.50000 STDIN      svennd       Eqw   03/24/2022 16:30:21                                    1        
     42 0.50000 hostname   svennd       Eqw   03/28/2022 09:25:26                                    1        

svennd@server:~$ qstat -explain c -j 42
==============================================================
job_number:                 42
exec_file:                  job_scripts/42
submission_time:            Mon Mar 28 09:25:26 2022
owner:                      svennd
uid:                        100172585
group:                      domain
gid:                        100000514
sge_o_home:                 /home/AD/svennd
sge_o_log_name:             svennd
sge_o_path:                 /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
sge_o_shell:                /bin/bash
sge_o_workdir:              /home/AD/svennd
sge_o_host:                 server
account:                    sge
mail_list:                  svennd@server.domain.be
notify:                     FALSE
job_name:                   hostname
jobshare:                   0
env_list:                   TERM=NONE
script_file:                hostname
binding:                    NONE
job_type:                   binary
error reason          1:      can't get password entry for user "svennd". Either user does not exist or error with NIS/LDAP etc.
scheduling info:            Job is in error state
```

The key here is : `can't get password entry for user. Either user does not exist or error with NIS/LDAP etc.`

This could be resolved by restarting gridengine-exec; However how lasting this issue will be, I don't know. [Source, webcache of google](https://webcache.googleusercontent.com/search?q=cache:d08peqXsNqUJ:https://github-wiki-see.page/m/rocksclusters/wiki/wiki/sun-gridengine-problems)

```
service gridengine-exec restart
```


Valuable sources during the debugging :
- [SGE documentation](http://www.softpanorama.org/HPC/Grid_engine/sge_queues.shtml)
- [gridengine installation guide](https://peteris.rocks/blog/sun-grid-engine-installation-on-ubuntu-server/)
- [gridengine installation failure fix](https://www.cloudsatoz.com/2021/04/19/gridengine-installation-simple-configure/)

Image by [tvick](https://unsplash.com/@tvick)
