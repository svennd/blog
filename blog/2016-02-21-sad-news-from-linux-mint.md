---
title: 'Sad news from Linux Mint : compromised ISO’s spread'
author: svennd

date: 2016-02-21T12:59:16+00:00
url: /sad-news-from-linux-mint/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
I'm a split-brain when it comes to Linux distro's I love the stability of Centos (RHEL,...) but Debian, Ubuntu and other friends have a way bigger community for desktop/laptop Linux. Of the Debian lineage I was a huge fan of #! (CrunchBang linux, now [BunsenLab Linux][1]) and Linux Mint. Mint cause its so simple and so workable, moving from Windows to Linux, Mint was a really good friend. Although I moved away from them a while ago, there are still some Mint users among my colleagues and I still consider it a rock solid, great distro! Yesterday 20, Februari, 2016 their web-server got hacked and the ISO was compromised. At this point there website is down, this is probably the best course of action. But info is vital at this point. In the compromised ISO, a IRC backdoor was placed linked to a East-Europe country.

<!--more-->

**What happened ?**

  * **linuxmint-17.3-cinnamon** was compromised, a backdoor [IRC BOT][2] was in the ISO and connected to _absentvodka.com._
  * The[ **Forum database** got compromised][3], so expect more spam and change your password -again-. <del>Although nothing is officially said about this yet.</del> [Its now officially.][4]
  * It seems **hackers still have some control** over the servers, so server was put offline. (linuxmint.com is offline)

<p style="text-align: left;">
  <strong>I use Linux Mint what should I do ?<br /> </strong>Unless you (re)installed from an ISO this Saturday no need to panic;  Check if you have a file <code class="EnlighterJSRAW" data-enlighter-language="null">/var/lib/man.cy</code>  if you have you have been compromised, if not, you should be safe. Content from man.cy now on github. You can check for this file using :
</p>

<pre>find / ! -readable -prune -name man.cy</pre>

**I have updated my distro on Saterday **  
At this point there is no reason to believe you are comprimised, the update process gets info from repositories, those are distributed and where not targeted on this attack, being up-to-date is the way to go !

<pre>sudo apt-get update && sudo apt-get upgrade</pre>

**I downloaded the ISO what to do ?**  
Remove the ISO and redownload the ISO again from P2P network/https and check the md5 (or better, sha256) hash. The correct [hashes][5] are :

<pre>6e7f7e03500747c6c3bfece2c9c8394f  linuxmint-17.3-cinnamon-32bit.iso
e71a2aad8b58605e906dbea444dc4983  linuxmint-17.3-cinnamon-64bit.iso
30fef1aa1134c5f3778c77c4417f7238  linuxmint-17.3-cinnamon-nocodecs-32bit.iso
3406350a87c201cdca0927b1bc7c2ccd  linuxmint-17.3-cinnamon-nocodecs-64bit.iso
df38af96e99726bb0a1ef3e5cd47563d  linuxmint-17.3-cinnamon-oem-64bit.iso</pre>

checking md5 can be done like this : ([for windows users][6])

<pre>md5sum linuxmint-17.3-cinnamon-32bit.iso</pre>

**The compromised checksum  :  
** If you got this hash its 100% a **bad version**, remove it.

<pre>7d590864618866c225ede058f1ba61f0 linuxmint-17.3-cinnamon-64bit.iso</pre>

**All valid md5/sha256 hashes  
** md5 hashes :

<pre>6e7f7e03500747c6c3bfece2c9c8394f  linuxmint-17.3-cinnamon-32bit.iso
e71a2aad8b58605e906dbea444dc4983  linuxmint-17.3-cinnamon-64bit.iso
ad46900208e69a952b3c4e1bc7f941e5  linuxmint-17.3-mate-32bit.iso
d3c0ef9d0c0c93ab7109fa2ef1db0c28  linuxmint-17.3-mate-64bit.iso
30fef1aa1134c5f3778c77c4417f7238  linuxmint-17.3-cinnamon-nocodecs-32bit.iso
3406350a87c201cdca0927b1bc7c2ccd  linuxmint-17.3-cinnamon-nocodecs-64bit.iso
d2b0e3867e7be494e5f6a20c15601e8b  linuxmint-17.3-mate-nocodecs-32bit.iso
2ba56a8589aa57063a999027c851cfe3  linuxmint-17.3-mate-nocodecs-64bit.iso
df38af96e99726bb0a1ef3e5cd47563d  linuxmint-17.3-cinnamon-oem-64bit.iso
1a6f14e00b74d9fdc20bd475865e0c3a  linuxmint-17.3-mate-oem-64bit.iso
6a42a8ec6ad050ada8478ba2f0d8586b  linuxmint-17.3-kde-32bit.iso
9fae1a87bebe4b57f6a587272f0cee3d  linuxmint-17.3-kde-64bit.iso
40a2056d20e67d6732d3fd679a6f5f0d  linuxmint-17.3-xfce-32bit.iso
729c92e3ef247bbc12104e6c14a2b95e  linuxmint-17.3-xfce-64bit.iso</pre>

to check using :

<pre>md5sum *.iso</pre>

sha256 hashes :

<pre>46b8a14826a53f4cacf56d1132a5184c2132f274aef8103e5e8e8cae9e1cfde0  linuxmint-17.3-cinnamon-32bit.iso
854d0cfaa9139a898c2a22aa505b919ddde34f93b04a831b3f030ffe4e25a8e3  linuxmint-17.3-cinnamon-64bit.iso
506a8e88c83cddc7fadd2b7c5bf25b7e6a15f028e1628004dcd6470084430f17  linuxmint-17.3-mate-32bit.iso
d02bfaae749db966778276a8ae364843c1ffb37b3e1990c205f938bda367ad2a  linuxmint-17.3-mate-64bit.iso
e61ed8f5df9283e86926fb7c414f36f7649ce716517093807a193aaf7d396bb8  linuxmint-17.3-cinnamon-nocodecs-32bit.iso
c149f3f57275e5d64bf0401d12eff5d021b92688dbd21cdbb4111cb3415eda17  linuxmint-17.3-cinnamon-nocodecs-64bit.iso
ba6c4f3e70929f3e90d03fb3063892085b7a0e829579dc0f48723e94a2bc6570  linuxmint-17.3-mate-nocodecs-32bit.iso
71604ef7479855213ae044e4c896f38249ea4bc567f0013bd0157080f3130941  linuxmint-17.3-mate-nocodecs-64bit.iso
48d82518a73962f9b5d9d61383a90132b64ee6fa489a67547468c136c8a27bfd  linuxmint-17.3-cinnamon-oem-64bit.iso
694bf952d68eb5a69560a756e578d85531be1498b08dd30aee6919c9139a7434  linuxmint-17.3-mate-oem-64bit.iso
be64bf240a47df03fedca1b8aeb9357896e3dedd55446a0f87eca4f638c9d28c  linuxmint-17.3-kde-32bit.iso
aa33bf286e92556163c335b258fe5cbd9f65f4ab8490e277fed94cf20d3920e4  linuxmint-17.3-kde-64bit.iso
cebff34e99b071d7237d2cfd2e24719f5a72e9e499a82d424007e850befc755b  linuxmint-17.3-xfce-32bit.iso
83c1796a37582bdea74117193cef369582d72093fd0b5278ae03016bd8685b04  linuxmint-17.3-xfce-64bit.iso</pre>

to check using :

<pre>sha256sum *.iso</pre>

Don't trust my word : check some download site. Like [belnet.be][7].

**Why did they hack Linux Mint ?  
** From what I gather pure financial gain. There doesn't seem to be a political agenda connected. Either way, the "security" being md5 hashes has not failed, although [md5 collisions are possible][8], it was not used here.

Stay safe !

The [original blogpost on Mint][5].

 [1]: https://www.bunsenlabs.org
 [2]: http://blog.malwaremustdie.org/2013/05/story-of-unix-trojan-tsunami-ircbot-w.html
 [3]: https://twitter.com/ydklijnsma/status/701331196769394688
 [4]: http://blog.linuxmint.com/?p=3001
 [5]: http://blog.linuxmint.com/?p=2994
 [6]: http://www.winmd5.com
 [7]: http://ftp.belnet.be/
 [8]: http://www.mathstat.dal.ca/~selinger/md5collision/
