---
title: Setting up Postfix TLS with Let’s Encrypt
author: svennd

date: 2016-02-12T14:05:50+00:00
url: /setting-up-postfix-tsl-with-lets-encrypt/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I'm not an expert in configuring mail servers. In fact I have never setup a dedicated mail server and there are no "simple", complete, updated online tutorials. Most articles are ending with : if you don't know how to do it, don't touch it, cause you will be in blacklist/spam before you can say 'Baboon bamboo' five times.

  ![A little encrypted Postfix mouse ?](/img/2016/02/25449528-1.gif)


They are most likely true, so I don't want to have a mail server, but my server is sending mails isn't it ? When people comment or respond I get mail and in return -I hope- when I respond people also get notified by this, through mail...  this magically works and I believe its postfix's doing. Now recently I get mails that are coming from a non encrypted mail server... bummer right ? Well Gmail, (Google, Alphabet) recently started marking these kind of mails with a red unlocked-lock, kinda the opposite of the green lock in-front of _https_. (see the url of svennd.be)

<!--more-->

&nbsp;

For example :

<img loading="lazy" class="alignnone wp-image-963 size-full" src="/img//2015/07/25446752-1.png" width="374" height="308" srcset="/img/2015/07/25446752-1.png 374w, /img/2015/07/25446752-1-300x247.png 300w, /img/2015/07/25446752-1-1x1.png 1w" sizes="(max-width: 374px) 100vw, 374px" />

While this is nothing to worry about **yet **the fact that google wants us to start encrypting the connection between 'mail' servers, its only a matter of time before they will force you to encrypt this. I don't think its that crazy, so I searched the web for a solution. I have found bits all over, but nothing straightforward, hence I will share my method, while my method might not be required or optimal, you get fancy nice encryption support on mail connections such as : (no green padlock sadly)

  ![now the mail was send using encryption (TLS)](/img//2015/07/25428192-1.png)

**Changes to postfix/main.cf**

The file you are looking for is _/etc/postfix/main.cf, _I changed quite some values, not all of them might be required.

These I entered / changed :

<pre>myhostname = svennd.be
mydomain = svennd.be
myorigin = $mydomain</pre>

I added these values :

<pre># logging
smtpd_tls_loglevel = 1

# Allow use of TLS but make it optional
smtp_use_tls=yes

# Disable SSLv2/3 as they are vulnerable
smtpd_tls_protocols = !SSLv2, !SSLv3
smtp_tls_protocols = !SSLv2, !SSLv3

# Insist on stronger ciphers
smtpd_tls_ciphers = high
smtp_tls_ciphers = high

# keys
smtp_tls_cert_file = /etc/letsencrypt/live/svennd.be/fullchain.pem
smtp_tls_key_file = /etc/letsencrypt/live/svennd.be/privkey.pem
</pre>

In case you don't have SSL yet, its free using [Let's Encrypt][1], and I even have a [tutorial up here for Centos 6.X][2]! Now SMTP is the tool that is used to <span style="text-decoration: underline;">send </span>mails, SMTP**D** is the part to <span style="text-decoration: underline;">receive</span> mails.  So in my case I only need the SMTP configuration. After these changes you can restart the service :

<pre>service postfix restart</pre>

Now you might wanne test this, for this purpose you can just use _sendmail_ which is included in Centos, and probably in most distro's as part of postfix.

<pre>sendmail recipient@gmail.com
From: myname@svennd.be
Subject : test mail
this is my test mail with no real content
.
</pre>

_note : the single dot is "end of mail"; after that enter and wait a few seconds (its being send)_

**Conclusion**

The next person who says mails are just text files send around... ow boy... anyways, setting up a mailserver remains a illusive part of the sysadmin task for me. However encrypting the server to server connection seems to work, thanks to the SSL certificates of Let's Encrypt.

**Reference**

  * Excellent read configuring [postfix SMTP][3].

 [1]: https://letsencrypt.org/
 [2]: https://www.svennd.be/lets-encrypt-with-centos-6/
 [3]: http://donnlee.com/2016/11/27/postfix-smtp-configuration-sending-relay-email-to-gmail-and-other-internet-mail-servers/
