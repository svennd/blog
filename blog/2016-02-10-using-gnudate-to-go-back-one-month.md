---
title: Using GNU/date to go back one month
author: svennd

date: 2016-02-10T08:59:53+00:00
url: /using-gnudate-to-go-back-one-month/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
GNU/date is really a nice tool, today I found this awesome option. (reading the man page ... ahum) In a shell script I run in a cron I make a monthly store. (/year/month/day.ext) I wanted to store last months in a single archive.

I store the backup file :

<pre>BACKUP_PATH=/mnt/database_backup/$(date +%Y)/$(date +%m)/</pre>

this makes an easy /2016/01/01.db, /2016/01/02.db, ... Now going back one month can be done with text ! Just tell date to go back "1 month ago". How awesome is that !

<pre>BACKUP_PATH=/mnt/database_backup/$(date +%Y)/$(date +%m --date '1 month ago')/</pre>

great for simple cronnies!