---
title: bare minimum samba share on Centos 8
author: svennd

date: 2020-02-17T15:17:43+00:00
url: /bare-minimum-samba-share-on-centos-8/
thumbnail: /img/2020/04/centos.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - samba

---
This is a quick guide on how I got a simple samba (4.10.x) share to work on Centos 8.1.1911 (Core) and Windows 10 Enterprise (1903). By god this should not be so hard, but it f*cking is.

#### Generic setup

Install samba :

<pre>yum install samba</pre>

edit <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/samba/smb.conf</code>

change the workgroup, netbios name and security, add log file and log level:

<pre>[global]
        workgroup = SERVERWORKGROUP
        netbios name = server
        security = user

        log file = /var/log/samba/%m.log
        log level = 1
</pre>

#### Create user & share

Create a user :

<pre>useradd machine</pre>

optional, if only used for samba, you can not get them a /home directory and no shell using :

<pre>useradd -M -s /sbin/nologin machine</pre>

In order to enable the user, they require a password, however this won't be used (unless if you gave them normal user shell & home dir)

<pre>passwd machine</pre>

Give them a samba password (used to authenticate) :

<pre>smbpasswd -a machine</pre>

Should give :

<pre>[root@server ~]# smbpasswd -a machine
New SMB password:
Retype new SMB password:
tdbsam_open: Converting version 0.0 database to version 4.0.
tdbsam_convert_backup: updated /var/lib/samba/private/passdb.tdb file.
Added user machine.
</pre>

**Enable the user :**

<pre>smbpasswd -e machine</pre>

Now once we done that, its time to create the share folder :

Create the share path :

<pre>mkdir /server/upload/machine</pre>

Give the new user permissions :

<pre>chown machine.machine /server/upload/machine</pre>

then add the share in the configuration; (smb.conf)

Add :

<pre>[nextseq]
        path = /server/upload/machine
        read only = no</pre>

now for the full smb.conf : test it !

<pre>testparm</pre>

<pre>[root@server upload]# testparm
Load smb config files from /etc/samba/smb.conf
Loaded services file OK.
Server role: ROLE_STANDALONE

Press enter to see a dump of your service definitions

# Global parameters
[global]
        log file = /var/log/samba/%m.log
        security = USER
        workgroup = MACHINEWORKGROUP
        idmap config * : backend = tdb


[machine]
        path = /server/upload/machine
        read only = No
</pre>

#### Firewall

Whether you use iptables or firewalld; you need to open some ports for samba :

<pre>firewall-cmd --permanent --add-service=samba
firewall-cmd --reload
</pre>

#### Start Service

Then we should be able to start the service and connect windows to it :

<pre>service smb start</pre>

note : I did not start nmb or winbind;

A happy ending for samba after all;

<pre>$ yum info samba
Last metadata expiration check: 0:26:43 ago on Mon 17 Feb 2020 03:52:39 PM CET.
Installed Packages
Name         : samba
Version      : 4.10.4
Release      : 101.el8_1
Architecture : x86_64
Size         : 2.4 M
Source       : samba-4.10.4-101.el8_1.src.rpm
Repository   : @System
From repo    : BaseOS
Summary      : Server and Client software to interoperate with Windows machines
URL          : http://www.samba.org/
License      : GPLv3+ and LGPLv3+
Description  : Samba is the standard Windows interoperability suite of programs for Linux
             : and Unix.

</pre>