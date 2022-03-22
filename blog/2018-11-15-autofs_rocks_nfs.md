---
title: Use Autofs on Rocks cluster to add NFS mounts
author: svennd

date: 2018-11-15T10:16:13+00:00
url: /autofs_rocks_nfs/
thumbnail: /img/2020/05/colors.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - autofs
  - nfs
  - rocks

---
Ordinarily I use "static" references in /etc/fstab to mount NFS shares to a server. Doing this on a Rocks Cluster however is a bit "hacky" and adapting every node can be automated by using <code class="EnlighterJSRAW" data-enlighter-language="null">rocks run host</code> however there is a alternative way. (not sure if its better 😉 ) but Rocks uses Autofs to load NFS mounts when they are required. So I felt brave and wanted to learn something new. This is the documentation on that journey.  ![](/img/2018/11/autofs.png)

###### 

<!--more-->

###### Add key map to auto.master

In order to make a new "mapping file" we need to add a directory to the server where AutoFS will be boss, note that it cannot be root (/). So in /etc/auto I added :

<pre># ghost creates empty dir
/storage /etc/auto.mounts --ghost --timeout=1200
</pre>

Interesting to note here is -ghost is creating the directory's if it fails to mount. In Rocks this is the default file :

<pre># cat /etc/auto.master
/share /etc/auto.share --timeout=1200
/home  /etc/auto.home  --timeout=1200</pre>

So homes are automatically mounted when a user runs a job.

###### Create the mapping file

Now we need to make a "key" based mount files; Since in last step I added /etc/auto.mounts, that is the name of the file, I don't know if  auto.* is a requirement, but I think its a good way, since they are located in /etc as loose files.

To add a NFS share, the template is :

<pre>on_server_dir -nfs_options server:/share</pre>

So a pretty simple example would be :

<pre>data  -rw,hard,intr,rsize=32768,wsize=32768,nfsvers=3 svennd.be/data</pre>

However, if you like me export multiple shares from one server, and you want them in a separate directory, the template is :

<pre>on_server_dir -nfs_options /subdir server:/share /subdir2 server:/share2 /subdir3 server2:/share</pre>

Useful to know is that lines can be split using "\" however make sure nothing is after this backslash, otherwise you will have errors. Talking about errors, debugging AutoFS can be done by stopping the service and manually run the AutoFS daemon :

<pre># stop the service
service autofs stop

# run service manually and verbose
automount -f -v</pre>

###### Push to the nodes

Since I don't want to make all the changes to the nodes manually we need to run :

<pre>rocks sync users</pre>

This will sync all the auto.master and some other files, sadly it does not pick up auto.mounts; So that we have to do manually :

<pre>/opt/rocks/sbin/411put /etc/auto.mounts</pre>

This will push the /etc/auto.mounts file to all nodes. After that we can just restart the server (reload won't work)

<pre>rocks run host "service autofs restart"</pre>

And voila it works 🙂

Resources :

  * [RedHat tutorial][1]
  * [ReadOnly nearest selector for AutoFS][2]

 [1]: https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/System_Administration_Guide/Mounting_NFS_File_Systems-Mounting_NFS_File_Systems_using_autofs.html
 [2]: https://docs.oracle.com/cd/E19455-01/806-0916/6ja8539g7/index.html#rfsrefer-86