---
title: windows 10 fall update and samba guest account
author: svennd

date: 2017-12-06T10:45:04+00:00
url: /windows-10-fall-update-and-samba-guest-account/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
  - windows
tags:
  - samba
  - windows

---
Suddenly a Linux server, -only- serving as 'open' samba share (guest account allowed) stopt working. I logged in and found samba working, no weird network issues, nothing. A mystery !

After a _service smb restart_ and a _reboot -h now _(sue me, uptime) I increased the log level of smb to level 3 this is done by changing _/etc/samba/smb.conf_ :

<pre>[global]
        workgroup = SAMBA
        security = user
        passdb backend = tdbsam

        [...]

        map to guest = Bad User
        log level = 3

        [... below come shares ...]
[data]
  path = /data
  [...]
  force user = testuser
  guest ok = yes</pre>

You can then follow what samba is doing at /var/log/samba/log.smbd (for Centos, after restarting the service) This is what I found :

<pre>[2017/12/05 11:10:37.388846,  3] ../source3/auth/auth.c:178(auth_check_ntlm_password)
  check_ntlm_password:  Checking password for unmapped user [DESKTOP-XXXXXX]\[svennsvenndDESKTOP-XXXXXX] with the new password interface
[2017/12/05 11:10:37.388886,  3] ../source3/auth/auth.c:181(auth_check_ntlm_password)
  check_ntlm_password:  mapped user is: [TEMPSTORAGE]\[svennd]@[DESKTOP-XXXXXX]
[2017/12/05 11:10:37.388985,  3] ../source3/auth/check_samsec.c:399(check_sam_security)
  check_sam_security: Couldn't find user 'svennd' in passdb.
[2017/12/05 11:10:37.389029,  2] ../source3/auth/auth.c:315(auth_check_ntlm_password)
  check_ntlm_password:  Authentication for user [svennd] -&gt; [svennd] FAILED with error NT_STATUS_NO_SUCH_USER
[2017/12/05 11:10:37.389077,  3] ../source3/auth/auth_util.c:1610(do_map_to_guest_server_info)
  No such user svennd [DESKTOP-XXXXXX] - using guest account
[2017/12/05 11:10:37.390215,  3] ../source3/smbd/server_exit.c:246(exit_server_common)
  Server exit (NT_STATUS_CONNECTION_RESET)</pre>

So in short windows tries to use my local account and fails, this is expected. Then samba gives me the permissions of a guest account. Weirdly enough after that samba reports NT\_STATUS\_CONNECTION_RESET, or more simply put "server exit". I tried to find more info on the recent patches using :

<pre>rpm -q --changelog samba-common-4.6.2-12.el7_4.noarch | less</pre>

At this time of writing the latest "feature change" was way back in march, this installation was newer so that could hardly be the issue.

<pre>* Fri Mar 31 2017 Guenther Deschner &lt;gdeschner@redhat.com&gt; - 4.6.2-0
- Update to Samba 4.6.2
</pre>

In the end, we did not find the issue in the samba server. The issue was the client, running a windows 10 up-to-date version (fall creators update). This was the only change, between a working and not working setup. So Windows must have changed some behavior ? Tested with a Windows 7 machine, this suspicion was confirmed, there it worked. A workaround for my case was to setup a username that the server does know on the client :

<pre>C:\Users\svenn&gt;net view \\shareserver
System error 53 has occurred.

The network path was not found.

C:\Users\svenn&gt;net use \\shareserver\data /user:testuser
The command completed successfully.


C:\Users\svenn&gt;net view \\shareserver
Shared resources at \\shareserver

Samba 4.6.2

Share name  Type  Used as  Comment

-------------------------------------------------------------------------------
data        Disk  (UNC)    storage
data_rgb    Disk           storage
The command completed successfully.
</pre>

And after that I can browse and access the share through explorer. While this for sure is not foolproof, for my case its enough. (single client to server)

This register setting n windows seems to be affecting this issue, thanks Dominik!
