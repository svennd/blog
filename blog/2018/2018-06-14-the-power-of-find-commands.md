---
title: The power of find commands
author: svennd

date: 2018-06-14T09:52:36+00:00
url: /the-power-of-find-commands/
thumbnail: /img/2018/06/find-150x150.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - find
  - linux

---
GNU [find][1] is a useful tool in any Linux user's toolbox. It can be used for simple searches up to a small processing tool. Here I share some of the useful examples I used in the past; As with anything on the web, test before running ! (most definitely those with -rm or delete flags!)

<!--more-->

##### Simple find

Simply find all PHP files :

<pre>find wordpress/ -iname "*.php"</pre>

recursively search, case insensitive to all files ending with *.php

##### AND && OR ||

<pre>find . -type f -name "*.php" -o -name "*.txt" -o -name ".csv"</pre>

This selects multiple extensions -o is the programming "OR or ||". Since all these are extensions, the -type f is a bit redundant but it means we only search for files.

##### user files

<pre>find . -user alice -exec chown svennd.TeamAlice "{}" \;

</pre>

Searching for all users owned by Alice, and changing ownership to Svenn in group TeamAlice.

##### Complex examples

Find all _*.tsv _larger 1Mb, compress them with the super fast [lz4][2] on high compression and remove the source file after this.

<pre>find . -name "*.tsv" -size +1M -exec lz4 -9 --rm '{}' \;</pre>

In the same line; compress all files ending with \*.fastq and gzip them, also they cannot end with \*.gz (in this case redundant but its an extra safety)

<pre>find . -type f -name "*.fastq" ! -name '*.gz' -exec gzip "{}" \;</pre>

Recursive remove all directory's matching the name *.tsv.index in a _rm or echo_ single command. This makes it possible to easily swap out _rm_ for _echo_ as a test.

<pre>find . -type d -name "*.tsv.index" -exec echo {} +
find . -type d -name "*.tsv.index" -exec rm -rf {} +</pre>

A combination of a few commands, calculate the storage use from all files size larger then 1M, with no hardlinks, ending with *.tsv.

<pre>find . -name "*.tsv" -size +1M -links 1 -print0 | du -hc | tail -n 1</pre>

(edit: might not work as intended)

<pre>find . -name "log_jobs" -exec du -hc {} +</pre>

&nbsp;

Find files, that are newer then 5 minutes :

<pre>find . -type f -mmin -5</pre>

and older :

<pre>find . -type f -mmin +5</pre>

Hard links are nice, but also a (enter curse-word) to track, luckily we have find to locate it :

<pre>find /data -samefile file.txt -xdev</pre>

This would find all the files that are exactly the same as file.txt (so only hard links, no soft links or copy's) considering hard links can only be in one file system its logical to add _-xdev _which tells find not to enter other file-systems since hard links can not be across file-systems. If you are also looking for soft links remove _-xdev_ and add _-L_

Generate a md5sum for every file in this current directory except files "mylog.log" and "md5.lst".

<pre>find . -type f ! -name "mylog.log" ! -name "md5.lst" -exec md5sum "{}" + &gt; md5.lst</pre>

&nbsp;

A quick and dirty way to find directories (=experiments) that have been made in the last 90 days, sorted on date (removing hard linked .save dirs) This is a sort.

<pre>find . -maxdepth 1 -name "*_machine_ID_*" -type d -ctime -90 | grep -v .save | sort -t_ -k 2</pre>

&nbsp;

Ignore certain files, can be done using ! -name "*file" for example. This finds all directories starting with 17, and not ending with .save (hard link for us) and shows the size of those directories.

<pre>find . -maxdepth 1 -name "17*" ! -name "*.save" -type d -exec du -hs '{}' +</pre>

&nbsp;

Count certain file type in a single directory (not recursively)

<pre>find . -maxdepth 1 -name "*.fastq" | wc -l</pre>

&nbsp;

 [1]: http://man7.org/linux/man-pages/man1/find.1.html
 [2]: https://github.com/lz4/lz4