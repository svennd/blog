---
title: rocks stuck at choose language
author: svennd

date: 2018-08-19T16:44:35+00:00
url: /rocks-stuck-at-choose-language/
thumbnail: /img/2020/05/aleks-marinkovic-HKNtySNAULI-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - rocks

---
While updating our network on the [_Rocks Cluster_][1], the nodes had to reinstall (this is default protocol). Now however the nodes got stuck during PXE (over the net automatic installation) on the setting of the language.

![install language Centos](/img/2018/08/centos_6_install_language.png)

This is annoying as it would mean connect a screen and a keyboard to every node to install. This however is an indication that something is wrong all together, however finding what proved a little bit tricky, that's why I share it here.

To find if you face the same issue, execute :

<pre>sudo -u apache /opt/rocks/bin/rocks list host profile compute-0-0</pre>

This will show the configuration the node pulled from the head-node.  In my case it looked like :

<pre>Traceback (most recent call last):
  File "/opt/rocks/bin/rocks", line 301, in &lt;module&gt;
    command.runWrapper(name, args[i:])
  File
"/opt/rocks/lib/python2.6/site-packages/rocks/commands/__init__.py",
line 2194, in runWrapper
    self.run(self._params, self._args)
  File
"/opt/rocks/lib/python2.6/site-packages/rocks/commands/list/host/profile/__init__.py",
line 301, in run
    for host in self.getHostnames(args):
  File
"/opt/rocks/lib/python2.6/site-packages/rocks/commands/__init__.py",
line 773, in getHostnames
    min,max = self.db.fetchone()
TypeError: 'NoneType' object is not iterable</pre>

While this should be a large XML file structure like. After allot of extensive google skills (and this 2013 topic -now dead-) I found out that a simple MySQL update had dropped the root password out of the global configuration, this can be found :

<pre>/opt/rocks/etc/my.cnf</pre>

a update, generally saves it here :

<pre>/opt/rocks/etc/my.cnf.rpmsave</pre>

it should look like this :

<pre>[mysqld]
user            = rocksdb
port            = 40000
socket          = /var/opt/rocks/mysql/mysql.sock
datadir         = /var/opt/rocks/mysql

[client]
user            = rocksdb
port            = 40000
socket          = /var/opt/rocks/mysql/mysql.sock
password        = &lt;password&gt;
</pre>

You don't have to restart the MySQL or the service, just let the node reboot and it will install properly 🙂

Good luck

 [1]: http://www.rocksclusters.org
