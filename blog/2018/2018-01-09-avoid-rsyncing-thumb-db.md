---
title: Avoid rsyncing thumb.db
author: svennd

date: 2018-01-09T10:07:26+00:00
url: /avoid-rsyncing-thumb-db/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - rsync

---
If your sync is syncing a bunch of thumb.db files from windows cause someone opened the directory, you can easely remove it from the sync :

<pre>rsync -avhn --exclude 'Thumbs.db' source destination</pre>

note : I added -n for copy/paste mistakes 🙂

if you got plenty of file types/extensions you want ignored/excluded use :

<pre>rsync -avhn --exclude-from '/opt/exclude_rsync_list.txt' source destination</pre>

an example of this file could be :

<pre>.DS_Store
Thumbs.db</pre>

happy syncing.