---
title: Centos 8 (Proxmox) + Active Directory authorization
author: svennd

date: 2020-09-07T14:49:24+00:00
url: /centos-8-proxmox-active-directory-authorization/
thumbnail: /img/2020/02/nightsky.png
categories:
  - Linux
  - SysAdmin
  - windows

---
So, after my [rage post][1] a few weeks back, I finally managed to let Centos 8 talk to AD server for authentication and authorization. (there is a difference); I have in the past "always" relied on legacy systems I did not fully understand; I set up [samba shares][2] using a windows domain before, but found it really complex and if it stopped working, I stressed out cause what lever to pull, no idea. This post won't change that, I just share how this time I got it to work on a test server, which is inherently a first step towards anything production worthy !

So to authenticate (login + password -> you are fine) and authorization (you are you, and you have access) using a Windows Domain AD, there are a few options out there and this does not make it much easier; Ill just touch on the ones I played with;

  * **Kerberos** : this one is actually easy to setup, but only provides authentication; which is pretty useless if you want users to login on a system; as users only can prove they are a user but not where they have or don't have access.
  * **LDAP** : Lightweight Directory Access Protocol,  it should be possible to set this up if your domain controller is AD, since LDAP is the protocol, and AD is the "program" (like nginx (AD) and HTTP (LDAP)); This is also the protocol that is being used to connect to other directory services such as FreeIPA, OpenLDAP, Red Hat Directory Service, ...
  * **AD** : this is directory services, that provides LDAP, kerberos, ... this is what we want to connect to tell our server that this user is oké to access this server.
  * **Winbind :** this is a alternative to sssd we use here;

**Requirement**

Since our test environment is in Proxmox (LXC); one very specific requirement is needed on the host :  we need a [privileged container][3].  If you forget this, you will be able to connect to AD, get all information all the test will succeed, but ssh will still fail (for a really hard to pin-point issue). In _unprivileged containers_ root will be mapped to a "normal" Linux user, which means if you break the security you are still only at user level of the host; While I see no reason why unprivileged containers should fail, I did not find a way to make it work. (if you did; please leave a comment!)

**Install & Setup**

First make sure the system is up-to-date; the hostname is set correctly; and fix the time. For me this was already correct, however in Linux containers, not all default options such as NTP are available, due to the fact you can't change kernel values. Make sure the time is right.

Since we want to login, openssh-server needs to be installed and running.

<pre>yum -y
yum install openssh-server
hostnamectl set-hostname server
timedatectl set-timezone Europe/Brussels
systemctl enable openssh-server
systemctl start openssh-server</pre>

Check that you can correctly resolve your domain; preferably take the DNS server from AD.

<pre>$ cat /etc/resolv.conf
</pre>

I used _sssd_ and _realm_, which are the most future proof solutions; ([winbind][4] seems to be somewhat)

<pre>yum install sssd realmd
</pre>

discover the realm :

<pre>[root@svennd ~]# realm discover ad.domain.be
ad.domain.be
  type: kerberos
  realm-name: AD.DOMAIN.BE
  domain-name: ad.domain.be
  configured: no
  server-software: active-directory
  client-software: sssd
  required-package: oddjob
  required-package: oddjob-mkhomedir
  required-package: sssd
  required-package: adcli
  required-package: samba-common-tools
</pre>

These required package need to be installed; (note : **required-package**)

<pre>$ yum install oddjob oddjob-mkhomedir sssd adcli samba-common-tools
</pre>

Join the domain, this can be as a normal user. (Administrator not required)

<pre>[root@svennd ~]# realm join ad.domain.be -U SvennD
Password for SvennD:
</pre>

<pre>[root@svennd ~]# realm list
ad.domain.be
  type: kerberos
  realm-name: AD.DOMAIN.BE
  domain-name: ad.domain.be
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: oddjob
  required-package: oddjob-mkhomedir
  required-package: sssd
  required-package: adcli
  required-package: samba-common-tools
  login-formats: %U@ad.domain.be
  login-policy: allow-realm-logins
</pre>

Set authselector

<pre>[root@svennd ~]# authselect select sssd with-mkhomedir
Profile "sssd" was selected.
The following nsswitch maps are overwritten by the profile:
- passwd
- group
- netgroup
- automount
- services

Make sure that SSSD service is configured and enabled. See SSSD documentation for more information.

- with-mkhomedir is selected, make sure pam_oddjob_mkhomedir module
  is present and oddjobd service is enabled
  - systemctl enable oddjobd.service
  - systemctl start oddjobd.service
</pre>

Let's do what they say and start oddjobs :

<pre>systemctl enable oddjobd.service
systemctl start oddjobd.service
</pre>

After that, you should be almost ready to go; I entered /etc/sssd/sssd.conf to adapt the default configuration :

<pre>[sssd]
domains = ad.domain.be
config_file_version = 2
services = nss, pam

[domain/ad.domain.be]
ad_domain = ad.domain.be
krb5_realm = AD.DOMAIN.BE
realmd_tags = manages-system joined-with-adcli
cache_credentials = True
id_provider = ad
krb5_store_password_if_offline = True
default_shell = /bin/bash
ldap_id_mapping = True
use_fully_qualified_names = False
fallback_homedir = /home/%u@%d
access_provider = ad
ad_gpo_ignore_unreadable = True
</pre>

Important changes :

<pre>use_fully_qualified_names = False (you can login with svennd instead of svennd@ad.domain.be)
ad_gpo_ignore_unreadable = True (broke the login)
fallback_homedir = /home/%u@%d (this can be [whatever](https://jhrozek.fedorapeople.org/sssd/git/man/sssd-ad.5.html))
</pre>

After that be sure to restart sssd :

<pre>systemctl restart sssd
</pre>

**Tests & Debug  
** Pretty much all the tutorials on the web, are really successful, but in reality, you most likely will fail 10 times, and succeed only after weeks of [pure horror][5]. Finding that one shitty flag/file/whatever. Here are some tools and methods I used to find the ultimate succes;

Login over ssh, with a fully qualified name:

<pre>ssh -l SvennD@ad.domain.be localhost
</pre>

But that will most likely tell you wrong password, even tho its completely something else; So a good start of debugging might be to login not using ssh :

<pre>su -l svennd
</pre>

Print user and group information for the specified USER, or (when USER omitted) for the current user.

<pre>[root@server ~]# id svennd
uid=123123132(svennd) gid=123123132(domain users) groups=123123132(domain users),123123132(epic_cool_guys)
</pre>

If everything seems fine, but you still can't login, try to check if you can check all info using the getent (Get entries from administrative database); This is what Linux will do during other operations :

<pre>[root@server ~]# getent passwd svennd
svennd:*:1914847771:1914800513:John Doe:/home/svennd@ad.domain.be:/bin/bash
[root@server ~]# getent shadow svennd
[root@server ~]# getent group "domain users"
domain users:*:1231321312:svennd,user1,administrator,user2,user3
</pre>

note : _getent shadow_ should be empty for domain users, but local users will return a hash; For group this will be the users that have been cached or have logged in on the system. (the server is not queried!)

Good luck and may the Windows-Linux operation god(s) be with you.

 [1]: https://www.svennd.be/centos-8-windows-domain-authentication/
 [2]: https://www.svennd.be/samba-login-using-windows-ad-on-centos-7/
 [3]: https://linuxcontainers.org/lxc/security/#privileged-containers
 [4]: https://www.redhat.com/en/blog/sssd-vs-winbind
 [5]: https://xkcd.com/979/
