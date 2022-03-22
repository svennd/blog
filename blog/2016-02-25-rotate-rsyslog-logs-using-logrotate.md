---
title: Rotate rsyslog logs, using logrotate
author: svennd

date: 2016-02-25T13:34:04+00:00
url: /rotate-rsyslog-logs-using-logrotate/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Ow boy, after [splitting of my logfile][1] the file kept on growing, and grep' kept on becoming slower, now I initially thought rsyslog also managed the rotation, but I'm wrong, in fact its [logrotate][2] that does the work. So lets rotate the heck out of that long file!

**Configuration**  
the vendor specific configurations can be found here : /etc/logrotate.conf  
For Centos 7.2 :

<pre>weekly
rotate 4
create
dateext

include /etc/logrotate.d</pre>

Its kind of self explaining but here goes, these are the "default" values for the files :

note : I use _logrotate 3.8.6 _ some options might not be available on older systems.

  * **weekly** : rotate weekly, options are : daily, weekly, monthly, yearly
  * **rotate** : the old logs are kept for **4** weeks (4 rotations, depending on when they rotate, monthly would keep 4 months)
  * **create** : after the log has been rotate create a new file
  * **dateext** : after rotation add date, by default a number would be used. (log.1,log2, log3)
  * **include /etc/logrotate.d** : this is the most important part, it will include the application specific rotates, since I already have it filtered to a separate file, this location is best used.

**Specific**  
So I created a file **/etc/logrotate.d/tape** with the following :

<pre>/var/log/tape.log {
    rotate 52
    weekly
    create
    missingok
    delaycompress
    compress
}</pre>

this means :

  * **rotate** : keep a log of 52, this is 1 year.
  * **weekly** : rotate weekly
  * **create** : create a new empty file, while the application would create one either way, its nicer to be sure its there.
  * **missingok** : don't throw an error when the log file is not there. (although we create one after rotation, so it would be weird)
  * **delaycompress** : now this is an important one, since I don't know when the logfile is in use, I don't wanne go and compress it, since this will make errors, this way, even tho an application can still write to the logfile even after its rotated.
  * **compress** : lets save some space right 🙂

**Test drive  
** Good thing you don't have to wait a [daily|weekly|monthly|yearly] for it to work, you can just test the configuration like this :

<pre>logrotate -v /etc/logrotate.d/tape</pre>

You can also force it to rotate it using the -f flag

<pre>logrotate -v -f /etc/logrotate.d/tape</pre>

One of the better man pages by the way! [Check man page here.][3]

Bam that's it! Happy rotating !

 [1]: https://www.svennd.be/rsyslog-separate-file-for-logging/
 [2]: http://www.linuxcommand.org/man_pages/logrotate8.html
 [3]: http://linux.die.net/man/8/logrotate