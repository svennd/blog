---
title: Samba login using windows AD on Centos 7
author: svennd

date: 2018-05-04T14:43:13+00:00
url: /samba-login-using-windows-ad-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - samba

---
![](/img/2018/05/Naamloze-tekening-1.png)I'm no expert on this, but I had to google everything together so many times, I made a soon-to-be-outdated half-ass guide on how to let users access a samba share on Linux using the windows domain controller "AD" (active directory) or at least how I got it to work. Let me know if it worked out for you or if you hit a brick wall. Perhaps we need to tune the sound a bit ;-).

#### 

#### Dependency's

They might be needed or not, I have no clue, just install them already.

<pre>yum install sssd realmd oddjob oddjob-mkhomedir adcli krb5-workstation openldap-clients policycoreutils-python samba samba-client samba-common samba-common-tools ntpdate ntp</pre>

[sssd][1], is a relatively new method of getting the system to talk to the AD server. Samba obviously is needed for creating the windows accessible shares. The last dependency might not be required but its good to make sure if you got issues its not because servers disagree on time/date. Hence, NTP will help set a same date between servers.

#### Connecting

Or better say lets "join" the dark side of windows. I'm not gone lie, this is pretty ugly, but in Kerbal Space Program's motto, any landing you can walk away from is called a succes.

First add the domain controller to <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/hosts</code> this ensures that every connection will go to the right server, irrelevant of DNS, since the hosts file has the highest priority.

<pre># cat /etc/hosts
123.123.123.123 mydomain.at.my.be mydomain
</pre>

_123.123.123.123_ should be the IP and _mydomain.at.my.be_ should be the full domain and the last is optional the alias for the domain.

Many guides will also adapt <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/resolv.conf</code> while I don't think its needed, we do not take risks here,  [resolv.conf][2] is used for looking up the DNS, for this server the domain controller is highly suggested.

<pre># cat /etc/resolv.conf

search my_domain.be
nameserver 123.123.123.123</pre>

Adapt as required.

The next step, is making sure the servers have the same time setup, this won't be an issue for many, but its good practice. Do a _ntpdate_ call to the domain server to get a fix.

<pre># ntpdate domain
4 May 15:47:01 ntpdate[17004]: adjust time server 123.123.123.123 offset 0.015744 sec
</pre>

Something similar should be shown, after that we can set the service up to take over. If there is a huge difference, perhaps add the domain controller as NTP server. This would be done in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/ntp.conf</code> as "_server domain iburst_".

<pre>systemctl enable ntpd.service
systemctl start ntpd.service</pre>

Then finally we are ready to join the domain, this is done using :

<pre>realm join --user=domain_admin mydomain</pre>

This hopefully, silently ads your computer to the domain (after login), or if it fails it spits errors. After this is silently successful, you will find the realm in the realm list.

<pre># realm list
domain.url
  type: kerberos
  realm-name: DOMAIN.url
  domain-name: DOMAIN.url
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: oddjob
  required-package: oddjob-mkhomedir
  required-package: sssd
  required-package: adcli
  required-package: samba-common-tools
  login-formats: %U
  login-policy: allow-realm-logins
</pre>

_note : slightly modified configuration shown._

#### Configuration

After we got initial connection, its time to setup the configuration, this is done in <code class="EnlighterJSRAW" data-enlighter-language="null">/etc/sssd/sssd.conf</code> . There are [multiple parameters][3] here that can be adapted here. One in particular pops up as having an annoying default set.

###### _use\_fully\_qualified_names_

By default this is set to **True**, domain users will be identified as "_name@domain_" instead of "_name_". Set **False**, to drop the _@domain_

<pre>use_fully_qualified_names = False</pre>

In a similar way, home directories have a _@ _symbol in the name. While this in itself is harmless it can be annoying in script or tools that can't deal with these symbols. Perhaps some people have multiple domains, but for me its never the case. So I tend to remove the domain entirely. Alternatively you could do something like /home/domain/user <code class="EnlighterJSRAW" data-enlighter-language="null">/home/%d/%u</code> . Below I use /home/user, such as native users on Linux system. Change <code class="EnlighterJSRAW" data-enlighter-language="null">/home/%u@%d</code> to <code class="EnlighterJSRAW" data-enlighter-language="null">/home/%u</code>

<pre>fallback_homedir = /home/%u</pre>

After edit(s), reload sssd :

<pre>systemctl restart sssd</pre>

To verify the connection is functional, you can check a random (non-local) AD user :

<pre># id svennd
uid=1406204049(svennd) gid=1406200519(enterprise admins) groups=1406200519(enterprise admins),1406200513(domain users),1406200512(domain admins),1406204598(bioinf users)</pre>

At this point, all AD users should be able login using SSH on the system. (if sshd is running)

#### Samba

OK, now users can login to the server over ssh, but we want to bring a samba share available; so install samba if you did not do this in the first part. Don't worry I will wait.

<pre>yum install samba samba-client samba-common</pre>

Now adapt the configuration mostly to your own wishes; This is how I use mine : (/etc/samba/smb.conf)

<pre># See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = DOMAIN # CAPITALS make it work, domain controllers need hearing aids
        security = ads # active domain server
        encrypt passwords = yes
        realm = domain_url # adapt to full domain url

        passdb backend = tdbsam # starting this will replace it for user_id issues

        printing = cups
        printcap name = /dev/null # mute annoying errors
        load printers = no 
        cups options = raw
[data]
        valid users = @"domain_group@domain_url"
        path = /data
        public = yes # everyone can see it (if you are able to login)
        writable = yes # evereyone can write here (if you are a valid_user)
        guest ok = no
</pre>

Important parts here are :

<pre>workgroup = DOMAIN</pre>

Workgroup has to be the domain.

<pre>security = ads</pre>

Sets the security as "Active Directory Server", domain won't work.

<pre>realm = domain_url</pre>

Full realm, you can find this using <code class="EnlighterJSRAW" data-enlighter-language="null">realm list</code>

For restriction you can change the valid users using this syntax :

<pre>valid users = @"domain_group@domain_url"</pre>

This would only allow users of that group, syntax works for domain groups, local groups just have @devs. Also individual users can be added. Like this :

<pre>valid users = @"domain_group@domain_url" @localgroup svennd alice</pre>

##### 

##### Pitfalls & debugging

1. Firewall / Iptables

During debugging shut them down and if everything is resolved put them back up. For the firewalld lovers (default) add samba as allowed ports :

<pre>firewall-cmd --permanent --zone=public --add-service=samba
firewall-cmd --reload</pre>

For [iptables][4].

2. SELinux

Ow god this again, yes!!! To check if SELinux is enabled, (yes by default, even on minimal) use _sestatus_ :

<pre># sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28</pre>

Unless you want to [disable selinux][5], you will require the typical voodoo SELinux talk; For any directory where you set a share you need to run :

<pre>chcon -t samba_share_t /dir</pre>

If you like to have home directories automatically generated if a domain users authenticates (/etc/samba/smb.conf)

<pre>[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes</pre>

You need to run :

<pre>semanage fcontext -a -t home_root_t /home
semanage fcontext -a -t user_home_dir_t /home
semanage fcontext -a -t user_home_t /home
restorecon -Rv /home</pre>

or some other magic.

After all that, you can start samba :

<pre>systemctl enable smb
systemctl enable nmb
systemctl start smb
systemctl start nmb</pre>

And that's it. Kudo's to all the slightly-out-date tutorials.

 [1]: https://en.wikipedia.org/wiki/System_Security_Services_Daemon
 [2]: https://en.wikipedia.org/wiki/Resolv.conf
 [3]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/sssd-ad
 [4]: https://www.cyberciti.biz/faq/what-ports-need-to-be-open-for-samba-to-communicate-with-other-windowslinux-systems/
 [5]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security-enhanced_linux/sect-security-enhanced_linux-enabling_and_disabling_selinux-disabling_selinux