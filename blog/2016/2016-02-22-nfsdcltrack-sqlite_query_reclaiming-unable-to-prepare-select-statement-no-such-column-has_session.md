---
title: 'nfsdcltrack: sqlite_query_reclaiming: unable to prepare select statement: no such column: has_session'
author: svennd

date: 2016-02-22T14:03:54+00:00
url: /nfsdcltrack-sqlite_query_reclaiming-unable-to-prepare-select-statement-no-such-column-has_session/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Buggers, again [nfsdcltrack][1]. This time its again a sqlite error :

<pre>nfsdcltrack : sqlite_query_reclaiming: unable to prepare select statement: no such column: has_session</pre>

Luckily, this time there is a workaround to remove the error. Thanks to [Zenon Panoussis][2].

open up the database :

<pre>sqlite3 /var/lib/nfs/nfsdcltrack/main.sqlite</pre>

add the field to the table clients :

<pre>alter table clients add column has_session INTEGER;</pre>

_note : I changed it from tinyint to integer, as sqlite doesn't know the difference internally anyway. (everything is INTEGER)_

Then just exit and restart the nfs service

<pre>.exit
systemctl restart nfs</pre>

A happy nfsdcltrack service again!

 [1]: https://www.svennd.be/nfsdcltrack-sqlite_insert_client-insert-statement-prepare-failed-table-clients-has-2-columns-but-3-values-were-supplied/
 [2]: https://bugzilla.redhat.com/show_bug.cgi?id=1285097