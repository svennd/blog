---
title: Create a backup job on bareos
author: svennd

date: 2017-03-10T15:27:37+00:00
url: /create-a-backup-job-on-bareos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
In the last posts, we [setup bareos][1] and connected [a client to the bareos server][2], there are however no backup jobs auto-magically added to the bareos server so that's our job. At first creating a job seems difficult and complex, but most of the options only become interesting in specific applications and a simple basic job can be configured pretty easy. That's what I am going to do now, create a backup job that will incremental backup the /etc directory of my Linux client.  
<!--more-->

#### Job requirements

First we need to understand the target of the job, this step is important, if you don't do it you might end up with something completely different due to the options possible with bareos.

  * run a backup of /etc **daily** during non-business hours, but before the nightly update.
  * To save some space, I would take **incremental updates**. That means I always check what changes have occurred since the last incremental update. This makes that if nothing changes 0 extra bytes are used.
  * **Disk storage**, we have a tape library, but lets start with normal disk storage

#### 

#### Schedule

Since we have a pretty well defined time of day to run the backup job, let's start with first creating the specific schedule. In bareos all the configurations are nicely filtered in config files. In the directory /etc/bareos since we are configuring jobs that will be "directed" by the director, we need to go in the director configuration : /etc/bareos/bareos-dir.d/ there we can find the schedule directory. There might already be some examples there. But let's create a new schedule :

<pre>nano /etc/bareos/bareos-dir.d/schedule/Nightly.conf</pre>

with the content :

<pre>Schedule {
  # name (required)
  Name = "Nightly"
  
  # run time
  # allot of options are available,
  # you can create multiple "Run =" times
  # for now lets keep it simple
  # this will run once a day, every day at 21:10
  Run = daily at 21:10
}</pre>

You can make very complex schedules, but let's start with something simple and easy. For more complex examples and options check the [bareos manuel][3]. Most important is the _Run_ part, where we define the run frequency and the run time. Since /etc is rather small, it can be ran daily at an arbitrary time, but let's do it outside work hours.  Before you forget, all config files should be owned by the bareos user :

<pre>chown bareos.bareos Nightly.conf</pre>

####  Fileset

Next we need to let bareos know what we want to have backed up, by default one of the filesets is already useful for Linux : LinuxAll. This fileset will backup all the root and all its subdirectory's. This however is way to much and for our storage servers would simply not be possible. So let's go ahead and create a new file :

<pre>nano /etc/bareos/bareos-dir.d/fileset/LinuxConfig.conf</pre>

with content :

<pre>FileSet {
  # name (required)
  Name = "LinuxConfig"

  # include directory
  Include {
    Options {
      # calculate a signature for all files
      # both MD5/SHA1 are available, this is definitly for long term storage
      # a good idea to activate, note that the hash ads a bit of storage overhead
      Signature = MD5
      
      # compress every file with compression software
      # best known are : LZ4/GZIP (see manual for the others)
      # LZ4 is super fast in both compression and decompression
      # I would activate this always.
      Compression = LZ4
      
      # if supported by the OS, the read time won't be adapted
      # this would generate a bunch of writes for no reason on the client machine.
      noatime = yes
    }
    # the directory we are including
    # note: no trailing slashes!
    File = /etc
  }
}</pre>

Most important parts is the <code class="EnlighterJSRAW" data-enlighter-language="null">Include { File = /etc }</code> this will make sure it includes /etc directory and all its subdirectory's (this is also an option, but on by default)

notable options here are _signature_, a great feature, cause you don't want to put back a corrupt backup ... (it happens) While it has been proven that MD5 hashes can be broken it still is extremely difficult to do. But if you have that fear, you can use SHA1 which is a bit more CPU intense and takes up 4 more bytes per file but more difficult to break.

Another important feature is _compression_, I like LZ4 cause its fast, the CPU penalty is small compared to the storage you can gain. Note that this is run on the client. (file-daemon on client side)

Last option I included is _noatime_, while most storage systems already have atime off. Some system might still have it on, but I don't see the point of knowing when a file has been read by a backup process.

#### Jobdefs

Next part is combining all the property's of a job that would execute the requirements we set. This is the 'default' setup we start from when making jobs. Real jobs are called jobs, and can overwrite these defaults.

<pre>JobDefs {
  # name (required)
  Name = "BackupLinuxConfig"
  
  # type can be backup/restore/verify
  Type = Backup
  
  # the default level bareos will try
  # can also be Full/Differential(since last full)/Incremental(since last incremental)
  Level = Incremental
  
  # the default client, to be overwritten by the job.conf
  Client = bareos-fd
  
  # what files to include/exclude
  FileSet = "LinuxConfig"
  
  # the schedule we just created
  Schedule = "Nightly"
  
  # where to store it
  Storage = File
  
  # the message reporting
  Messages = Standard
  
  # the pool where to store it
  Pool = Incremental
  
  # the higher the value priority the lower it will be dropped in the queue
  # so for important jobs priority=1 will run first
  Priority = 10
  
  # the bootstrap file keeps a "log" of all the backups, and gets rewritten every time a 
  # full backup is made, it can be used during recovery
  Write Bootstrap = "/var/lib/bareos/%c.bsr"
  
  # in case these value's get overwritten
  # define where would be a good pool to write 
  # note that full backup will be used atleast once because no full
  # backup will exist
  Full Backup Pool = Full
  Differential Backup Pool = Differential
  Incremental Backup Pool = Incremental
}</pre>

The client is here bareos-fd, which is a default configured client pointing to its own installation. The fileset and schedule we just created is now being referenced here. Since all backups start from a "full" backup, there needs to be a backup pool.

Now that we have a good "sketch" (jobdef) for our backup job, creating the real backup is easy.

#### Job

Getting the final job to work is easy, just reference the jobdefs and overwrite the value's you want changed.

<pre>nano /etc/bareos/bareos-dir.d/job/svennd-etc-backup.conf</pre>

with content :

<pre>Job {
  # required
  Name = "svennd-etc-backup"
  
  # the default settings
  JobDefs = "BackupLinuxConfig"
  
  # overwrite the client here
  Client = "svennd"
}</pre>

And that's it.

#### Running the job

Obviously the job will start every day at 21:10, but as a test, its great if you can just start to run it now. Before you can start, be sure to check that all files you created are owned by _bareos_ user if not :

<pre>chown bareos.bareos file.conf</pre>

Then we can go to the _bconsole _and reload the configuration, this is **required**. If you don't do it only at the next restart of _bareos-dir_ will the new configuration be picked up.

<pre>bconsole</pre>

and run _reload _:

<pre>*reload
reloaded
</pre>

Once this worked and no errors where reported you can run the job we just made:

<pre>* run job=svennd-etc-backup
Using Catalog "MyCatalog"
Run Backup job
JobName:  svennd-etc-backup
Level:    Incremental
Client:   svennd
Format:   Native
FileSet:  LinuxConfig
Pool:     Incremental (From Job IncPool override)
Storage:  File (From Job resource)
When:     2017-03-10 15:48:01
Priority: 10
OK to run? (yes/mod/no): yes
Job queued. JobId=3</pre>

_note : although I specify the jobname here, you can just run "run" and you will be prompted for the exact job you wish to run._

The log would report after a bit :

<pre>10-Mar 10:21 bareos-dir JobId 1: No prior Full backup Job record found.
10-Mar 10:21 bareos-dir JobId 1: No prior or suitable Full backup found in catalog. Doing FULL backup.
10-Mar 10:21 bareos-dir JobId 1: Start Backup JobId 1, Job=lungo-01-etc-backup.2017-03-10_10.21.46_43
10-Mar 10:21 bareos-dir JobId 1: Created new Volume "Full-0001" in catalog.
10-Mar 10:21 bareos-dir JobId 1: Using Device "FileStorage" to write.
</pre>

As I said before the first backup would be a full one, and the next one, should start being incremental. If we run the job command a few times (you can use arrow up like in bash) and login in the web interface, under _jobs:_

[![bareos backup incremental overview in webinterface](/img/2017/03/bareos_svennd_backup-1024x280.png)][4]

And voila our first client is being backed up. Its should now be pretty easy to add more clients and get them backed up (just add more jobs under the job/ and overwrite the client). In the next post, we will look at how the backups are stored and how we can fine tune this setup. The target of backups of course is restoring the data, so that's also to come...

 [1]: https://www.svennd.be/installing-bareos-on-centos-7/
 [2]: https://www.svennd.be/adding-a-linux-client-to-bareos/
 [3]: http://doc.bareos.org/master/html/bareos-manual-main-reference.html#x1-1380009.4
 [4]: /img/2017/03/bareos_svennd_backup.png