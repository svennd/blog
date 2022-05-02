---
title: 'nfsdcltrack: sqlite_insert_client: insert statement prepare failed: table clients has 2 columns but 3 values were supplied'
author: svennd

date: 2016-01-21T13:36:04+00:00
url: /nfsdcltrack-sqlite_insert_client-insert-statement-prepare-failed-table-clients-has-2-columns-but-3-values-were-supplied/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
<p>Welcome readers of /var/log/messages, who have updated your server to the latest version of nfs-utils (at this writing : nfs-utils-1.3.0-0.21.el7).</p>
<pre># yum info nfs-utils
Name        : nfs-utils
Arch        : x86_64
Epoch       : 1
Version     : 1.3.0
Release     : 0.21.el7
Size        : 1.0 M
</pre>
<p>The full error message in /var/log/messages</p>
<pre>Jan 21 14:00:23 mocca nfsdcltrack[]: sqlite_insert_client: insert statement prepare failed: table clients has 2 columns but 3 values were supplied</pre>
<p>Its not a critical error, but the latest version of nfs-utils has it. Redhat already has a [few](https://bugzilla.redhat.com//show_bug.cgi?id=1285097) [bug](https://bugzilla.redhat.com/show_bug.cgi?id=1298320) reports on it, they even have a [hidden solution](https://access.redhat.com/solutions/2065873). Which I despise heavily, As I gather from the bug reports, its downgrading to an earlier version of nfs-utils. This I have never done before, but I would think it is <code class="EnlighterJSRAW" data-enlighter-language="null">yum downgrade nfs-utils</code> this however doesn't do much. From what I gather from the error message, some sqlite database gets a statement that has a slightly changed table.</p>
<p>The [nfsdcltrack ](http://manpages.ubuntu.com/manpages/trusty/man8/nfsdcltrack.8.html)tool is not a critical application on my machine, it seems to be only important when the service crashes or a reboot is done.</p>
<p>Anyway at this point the advice is : ignore the message. Damn Redhat on its hidden solution policy!</p>
<p>I got this message on a machine with 2 soft mounts.</p>
<p>Server :</p>
<pre>cat /etc/exports
/home/incoming 000.000.00.0(rw,no_root_squash)
/home/incoming 000.000.00.0(rw,no_root_squash)
</pre>
<p>note : ip's removed.</p>
<p>Client :</p>
<pre>servername:/home/incoming on /tape type nfs (rw,soft,vers=4,addr=000.000.00.0,clientaddr=000.000.00.0)</pre>
<p>note : ip's removed.</p>
