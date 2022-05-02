---
title: Bareos fully remove Client
author: svennd

date: 2019-05-08T09:07:56+00:00
url: /bareos-fully-remove-client/
thumbnail: /img/2020/05/pc_2.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - bareos

---
After adding a bunch of clients, we realized that a good naming convention was required and that some funny names, where not the best choice after all. So we changed them. However, Bareos will remember those Jobs ran against the old Client. The 'new' name will be seen as a new client; Some of the scripts we made however found it annoying that some clients where not backed up for weeks. Basically cause they where no longer existing. There is however a way to completely remove them from database.

  * "Restore" the original configuration, or create a new client in the configuration with the old name; (Address / Password does not have to be correct, we just need a name in the configuration)
  * in bconsole : <pre>*purge jobs client=SVENND

This command can be DANGEROUS!!!

It purges (deletes) all Files from a Job,
JobId, Client or Volume; or it purges (deletes)
all Jobs from a Client or Volume without regard
to retention periods. Normally you should use the
PRUNE command, which respects retention periods.
This command requires full access to all resources.
Using Catalog "MyCatalog"
Begin purging jobs from Client "SVENND"
Found 19 Jobs for client "SVENND" in catalog "MyCatalog".
Purge (yes/no)? yes
</pre>

  * After that, the client record will be orphaned, as nothing is ran for it; Now you can clear the database using : <code class="EnlighterJSRAW" data-enlighter-language="null">bareos-dbcheck -v -f</code>

<pre>[root@bareos ~]# bareos-dbcheck -v -f
Hello, this is the Bareos database check/correct program.
Modify database is on. Verbose is on.
Please select the function you want to perform.
     0) Quit
     1) Toggle modify database flag
     2) Toggle verbose flag
     3) Check for bad Filename records
     4) Check for bad Path records
     5) Check for duplicate Path records
     6) Check for orphaned Jobmedia records
     7) Check for orphaned File records
     8) Check for orphaned Path records
     9) Check for orphaned FileSet records
    10) Check for orphaned Client records
    11) Check for orphaned Job records
    12) Check for all Admin records
    13) Check for all Restore records
    14) Run ALL checks
Select function number: 10
Checking for orphaned Client entries.
Found 1 orphaned Client records.
Print them? (yes/no): yes
Orphaned ClientId=8 Name="SVENND"
Deleting 1 orphaned Client records.
Deleting: DELETE FROM Client WHERE ClientId=8
</pre>

Of course the data of those users are still in the filesystem (or tape) for me that is not really an issue, so I did not look into it.