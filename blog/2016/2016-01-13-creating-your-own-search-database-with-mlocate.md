---
title: Creating your own search database, with mlocate.
author: svennd

date: 2016-01-13T14:04:51+00:00
url: /creating-your-own-search-database-with-mlocate/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
Creating your own search database, not with blackjack and most definitely not with hookers but with _[mlocate][1]_. _Locate_ is one of the possible search options in Linux/GNU. Locate is part of the [_mlocate_][1] package, [this package contains][2] _locate_ binary. Locate is a database backed search, as such its faster then _find_ if you search over a large amount of indexed directories. The disadvantage is that you have to first build the database in order to search it.

I believe this is done on some distro's every night, (cron) if the machine is offline during that time the database does not get updated. The _mlocate_ package has a tool _updatedb _that can be ran to update the 'central' database. Its however also possible to create a database for, an external drive, this could be handy if you wanne see if the file is there, while the drive itself is not mounted. (or not even in close proximity to a computer) For me, I wanted to make [tapes][3] searchable, even when they where off the machine, in a top-secret-black-site location. (aka a box behind the server)

This can be done easily with _updatedb_ & _locate_.

<pre class="EnlighterJSRAW" data-enlighter-language="shell"># create the database
# the -l 0 sets the updatedb to ignore permissions 
updatedb -l 0 -U /media/external_drive -o external_drive.db

# search a file
locate -d /locate/external_drive.db IsMyImageHere

# limit the search to 10 items
locate -d /locate/external_drive.db -n 10 IsMyImageHere

# show stats on this database
locate -d /tape/external_drive.db -S
Database /tape/external_drive.db:
 92,709 directories
 7,440,981 files
 762,003,203 bytes in file names
 134,485,291 bytes used to store database

# speed showoff for database above :
time locate -d /tape/external_drive.db not_a_file

real 0m6.843s
user 0m6.782s
sys 0m0.061s

</pre>

~7 seconds for a  92,709 directories, 7,440,981 files search ... pretty nice 🙂

 [1]: http://linux.die.net/man/5/mlocate.db
 [2]: https://www.svennd.be/what-files-are-in-a-rpm-package/
 [3]: https://en.wikipedia.org/wiki/Linear_Tape-Open