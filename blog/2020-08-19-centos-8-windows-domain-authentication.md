---
title: Centos 8 + Windows domain authentication
author: svennd

date: 2020-08-19T14:01:01+00:00
url: /centos-8-windows-domain-authentication/
thumbnail: /img/2020/05/spacex.jpg
categories:
  - Linux
  - SysAdmin

---
How complex is joining a windows domain to authenticate on a Centos 8 server? very freaking hard. Welcome in 2020, everything is on fire and we are still locked up.

This is a collection of debug attempts to get my Proxmox container, test Centos 8 server authenticate against a domain. For ssh access.

&nbsp;

**kinit**

normal use :

<pre>kinit user@domain</pre>

**Error : Invalid UID in persistent keyring**

<pre>kinit: Invalid UID in persistent keyring name while getting default ccache</pre>

Solution : comment `# default_ccache_name = KEYRING:persistent:%{uid}` on  
`/etc/krb5.conf`  
by [brunowego][1]

note : [this solution][2] from RedHat, is just a syntax error. (not a real solution)

&nbsp;

**kinit: KDC reply did not match expectations while getting initial credentials**

This happened during test of kinit -v

<pre>kinit -V svennd@ad.domain.com
Using default cache: /tmp/krb5cc_0
Using principal: svennd@ad.domain.com
Password for  svennd@ad.domain.com
kinit: KDC reply did not match expectations while getting initial credentials
</pre>

The issue was here I had to use AD.DOMAIN.COM

**kinit: Password incorrect while getting initial credentials**  
wrong password during kinit -v user@domain.com

<pre>klist

Ticket cache: FILE:/tmp/krb5cc_0  
Default principal: svennd@DOMAIN

Valid starting Expires Service principal  
08/19/2020 16:50:08 08/20/2020 02:50:08 krbtgt/DOMAIN@DOMAIN  
renew until 08/25/2020 16:50:08
</pre>
&nbsp;

 [1]: https://github.com/liggitt/auth-proxy/issues/5
 [2]: https://access.redhat.com/solutions/2630741
