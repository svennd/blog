---
title: Remove user password
author: svennd

date: 2016-03-10T18:04:32+00:00
url: /remove-user-password/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Setting a user password is easy :

<pre>passwd backup_user</pre>

This is easy when you setup password less setup for server to server copy :

(server 1)

<pre>ssh-keygen -t rsa
ssh-copy-id user@remote</pre>

now you need a password on user@remote

(server 2)

<pre>passwd user</pre>

after it, you can "delete" the password again using :

<pre>passwd -d user</pre>

Great little trick 🙂