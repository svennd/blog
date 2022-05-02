---
title: Enhance backup schedule for Bareos
author: svennd

date: 2017-05-21T08:55:06+00:00
url: /backup-schedule-bareos/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
tags:
  - bareos

---
In the [previous article][1] on Bareos, we setup a quick and dirty backup job to run every night. This was pretty easy, but it has some flaws. (1) the first flaws, -after a full backup- only increment backups are created, forever. This makes it difficult to get a restore going down the line, as all the incremental backups need to poke the initial full backup before you can start recovering. (2) A second flaw, we ran backups but never checked if we can restore them. We need to take into account the Schrodinger's backup : "The condition of any backup is unknown until a restore is attempted". Perhaps a bit out of this scope but (3) we did not look where the backups are stored, and how they are being stored. There are plenty of options in Bareos, so let's take a look and fine tune the backup setup.

<!--more-->

##### Incremental forever

While [_incremental forever_][2] is in fact a backup model, there is a large overhead on getting everything back together, short term it is great, but long term we are facing some issue's. So let's look back on <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/bareos/bareos-dir.d/schedule/Nightly.conf</code> This is where our schedule is set, while for storage sake, incremental backups are a blessing, we should do a full and differential backup now and again.

We took the most simple approach on our backup, setting only the time (21:10) and the frequently (daily) to run the schedule _Nightly._

<pre>Schedule {
  Name = "Nightly"
  Run = daily at 21:10
}</pre>

We can enhance this by using _Job overrides,_ this permits overriding pretty much everything. This however can make things complex, so don't overdue it. Since I would like to have a full backup sets, let's add this first.

<pre>Schedule {
  Name = "Nightly"
  Run = daily at 21:10
  Run = Level=Full 1st sat at 20:10
}</pre>

_note : since incremental are a incremental backup since the last full backup, it's smart to make the full backup before doing the daily incremental._

So now it should make a extra full backup every first Saturday of the month at 20:10. We could further enhance our backup by adding a differential backup. This will keep all changes since the last full backup.

<pre>Schedule {
  Name = "Nightly"
  Run = daily at 21:10
  Run = Level=Full 1st sat at 20:10
  Run = Level=Differential 2nd-5th sat at 20:10
}</pre>

Now a backup will be made :

  * Every night at 21:10, a incremental
  * Every first Saturday of the month, a full backup
  * Every second, third, fourth and if there is a fifth, Saturday of the month, a differential backup

This means that in worst case 8 backups (1 full + 1 differential + 6 incremental) are needed to do a restore.

There is a more advanced feature we can use; there is a modulo operator. For example :

<pre>Run = Level=Differential w01/w02 sat at 22:10</pre>

This would run every odd week (1,3,5,7,..). Similar we can create a even week Run target <code class="EnlighterJSRAW" data-enlighter-language="null">Run = Level=Full w02/w02 sat at 22:10</code>

Considering the elegance of this method, I have gone with this method. Resulting in this Nightly schedule definition :

<pre>Schedule {
        Name = "Nightly"
        Description = "Do a daily backup, during closing hours"
        Run = daily at 21:10

        # additional, run a full backup every odd week
        Run = Level=Full w01/w02 sat at 22:10

        # additional, run a diff. backup every even week
        Run = Level=Differential w02/w02 sat at 22:10
}</pre>

Of course this comes at a price, for /etc this price is very small, every odd week I create a full backup and every even week a differential backup. Now to activate these changes, you could restart Bareos, or reload the configuration in bconsole.  A great way to check if it works as you intended is checking the status of scheduler :

<pre>*reload
reloaded
*status scheduler schedule=Nightly days=100
Scheduler Jobs:

Schedule Jobs Triggered
===========================================================
Nightly
   [...]
====

Scheduler Preview for 100 days:

Date Schedule Overrides
==============================================================
Sun 21-May-2017 21:10 Nightly
Mon 22-May-2017 21:10 Nightly
Tue 23-May-2017 21:10 Nightly
Wed 24-May-2017 21:10 Nightly
Thu 25-May-2017 21:10 Nightly
Fri 26-May-2017 21:10 Nightly
Sat 27-May-2017 21:10 Nightly
Sat 27-May-2017 22:10 Nightly Level=Differential
Sun 28-May-2017 21:10 Nightly
Mon 29-May-2017 21:10 Nightly
Tue 30-May-2017 21:10 Nightly
Wed 31-May-2017 21:10 Nightly
Thu 01-Jun-2017 21:10 Nightly
Fri 02-Jun-2017 21:10 Nightly
Sat 03-Jun-2017 21:10 Nightly
Sat 03-Jun-2017 22:10 Nightly Level=Full
Sun 04-Jun-2017 21:10 Nightly
Mon 05-Jun-2017 21:10 Nightly
Tue 06-Jun-2017 21:10 Nightly
Wed 07-Jun-2017 21:10 Nightly
Thu 08-Jun-2017 21:10 Nightly
Fri 09-Jun-2017 21:10 Nightly
Sat 10-Jun-2017 21:10 Nightly
Sat 10-Jun-2017 22:10 Nightly Level=Differential
[...]</pre>

As you see, every odd week a full backup is made, and every other week a differential is made. In next article I will dig deeper on how to restore my /etc in case someone did a <code class="EnlighterJSRAW" data-enlighter-language="null">rm -rf /etc</code> or <code class="EnlighterJSRAW">echo &gt; /etc/my_important_conf_file.conf</code>

 [1]: https://www.svennd.be/create-a-backup-job-on-bareos/
 [2]: https://en.wikipedia.org/wiki/Incremental_backup#Incrementals_forever