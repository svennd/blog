---
title: Using ZOHO SMTP mail on wordpress
author: svennd

date: 2017-01-30T18:23:29+00:00
url: /using-zoho-smtp-mail-on-wordpress/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin
  - webdev

---
After three hours reading up on <span id="12" class="osc_tag_system osc_error_blue">configuring</span> postfix/dovecot/... I finally thrown in the towel. There are some resources on the interwebs on how to setup a clean postfix on Centos/Linux, but they are<img src="/img/2017/01/zoho-download-300x110.png" /> dated or they [just throw in a configuration][1] file without explanation, and while I eventually got postfix mail to work. Sending a mail was slow (load time, no idea why) and gmail/google showed the mail being from _root@**linux\_host\_name**.svennd.be _which I did not like, since I wanted it to come from _no-reply@svennd.be_. On top of that, google put it in the spam directory, even after I put a valid SPF record. (for some reason this also wasn't accepted)

&nbsp;

So I gave it my best shot, it's time to accept defeat. Instead of trying to do it myself, I used [zoho mail][2] (referral link), this article is about how I set up mailing using the free service of zoho on my wordpress blog.

<!--more-->

### Registration zoho

First you need to make an [account][2] with zoho and allow it to verify your domain name, similar to google's webmaster system; either put a html file in the root of your website (html) or put a CNAME record in the DNS or TXT in DNS. I chose for a TXT record, but if you're in a hurry, the html method is allot faster. (no need to wait for a DNS update) However note that you will need to change/add a few DNS records irrelevant of what method you choise.  So while you are at your dns management module add/adapt the following records : (they will eventually be asked by the setup of zoho)

MX records, used for mailing; These two records will send mails to zoho, the priority can be any number, but they cannot be equal.

  * type : MX name : @ server : mx.zoho.com priority : 10
  * type : MX name : @ server : mx2.zoho.com priority : 20

SPF record, used to verify you allow mails to be send from this mail. These set the SPF record, some older servers might not recognize the specific SPF record and will search for TXT with SPF content. note that ~ (tilde) is a _soft-fail_, meaning if the mail does not come from zoho servers, will be marked as "not authentic" and are most likely spam. After a testing period, you can change it to - (minus) to set it to (hard)fail and reject e-mails not coming from zoho.

  * type : SPF name : @ content : v=spf1 mx include:zoho.com ~all
  * type : TXT name : @ content : v=spf1 mx include:zoho.com ~all

&nbsp;

The following are optional, and also domain/account specific. (use the setup by zoho)

(optional) zoho verification, there are currently three options. (TXT, CNAME, HTML)

  * type : TXT name : @ content : (domain specific)

(optional) DKIM : DomainKeys Identified Mail, is a safety measure to make sure nobody is able to spoof you're server/mail. by putting a public key in the DNS, and sending a private key in each mail header, the (receiving) client can validate that the key-pair matches and is from you.

  * type : TXT name : (name)._domainkey.(domain.ext) content : (domain specific)
      * This is the DKIM, optional, but good practice and a good pointer for anti-spam

_note: I use cloudflare, that recognizes @ as domainname.ext (your mileage may vary)_

![dns setup zoho mail](/img/2017/01/dns-300x151.png)


### Setup WordPress SMTP

Now that we can use zoho's mail servers; It's time to setup WordPress to use SMTP servers instead off falling back on the internal PHP mail() function. I used a plugin named : [Easy WordPress SMTP][3] ([plugin on wordpress.com repo][4]) After install there is a new option under Settings->Easy WP SMTP

I used these settings :  The SMTP username and password, are in fact an account you create (since I only have one, its that one)

![](/img/2017/01/smtp.png)

And that's it. Of course you should test, by creating a test mail.  If you don't wane login to the zoho mail, you can also forward the mail to a existing mail address. This can be done here : mail settings -> access old version for -> email forwarding and POP/IMAP.

https://mail.zoho.com (after login)

### Testing : mail-tester.com

I found this [great free service][5] to test and get feedback; You can use the testing mechanism in the WP plugin. You send a mail to the mail address given by them, and if all is fine this should be the result : (or similar)

![mail tester score 9/10](/img/2017/01/mail_tester-300x189.png)

Tada a working fairly spam protected mail solution for WordPress 🙂

&nbsp;

 [1]: https://www.digitalocean.com/community/tutorials/how-to-install-postfix-on-centos-6
 [2]: https://zm4.bz/3lN8jaMA
 [3]: https://wp-ecommerce.net/easy-wordpress-smtp-send-emails-from-your-wordpress-site-using-a-smtp-server-2197
 [4]: https://nl-be.wordpress.org/plugins/easy-wp-smtp/
 [5]: https://www.mail-tester.com
