---
title: Passwordless ssh login
author: svennd

date: 2016-05-03T14:24:50+00:00
url: /passwordless-ssh-login/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Being able to login without typing a password is not only more efficiently but one could argue its also safer. Its one of those thing you do once, and it just keeps on working, however I find myself asking how the heck did I fix this again. Well for my future self, this is how its done : logging in on Linux without using a username/password request.

**Requirements**  
I think most common distributions have this installed by default, but if not, you need the [openssh package][1]. For Centos that's : **openssh**

<pre># yum install openssh
Package openssh-6.6.1p1-25.el7_2.x86_64 already installed and latest version
Nothing to do</pre>

On Debian's side : **openssh-client**

<pre># apt-get install openssh-client
openssh-client is already the newest version.</pre>

**  
Creating the keypair**  
Is as easier then it sounds, **ssh-keygen -t rsa  
** 

<pre># ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
74:23:------------:91:c2:b2:f0:4f root@server
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
|                 |
+-----------------+
</pre>

If you leave the request for password empty, it won't ask you for a password and be the easiest, (which I use), however if you lose the machine (virtual or physically) the access is compromised. You have been warned.

**Copy'ing the key to the remote server**  
Now we created a public (_/root/.ssh/id_rsa.pub_) and private key (_/root/.ssh/id_rsa_), its time to send over the public key to the server, I use two methods, as the first method depends on the binary **ssh-copy-id** which is not available on all systems. (mostly older systems don't have it)

<pre>ssh-copy-id -i /root/.ssh/id_rsa.pub user@server</pre>

The -i is for identity_file, not case insensitive ... user@server,  know that this was done for a root user, if you do it as a different use adapt paths.

The second method, is just copy'ing the key directly to the authorized_keys using command line from the first server, this only works when in fact you can login with a password, in fact it happens a some [users don't have a password][2]. Then you really need to manually do this step, if they have, you can just let Linux copy & paste it for you.

<pre>cat /root/.ssh/id_rsa.pub | ssh root@server 'cat &gt;&gt; /root/.ssh/authorized_keys'</pre>

That's it ! You can now login w/o using username/password login! Also rsync & scp will now stop bothering you for passwords !

&nbsp;

&nbsp;

 [1]: http://www.openssh.com
 [2]: https://www.svennd.be/remove-user-password/