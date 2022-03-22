---
title: etckeeper setup on Centos 7
author: svennd

date: 2015-12-15T11:48:05+00:00
url: /etckeeper-setup-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - SysAdmin

---
[etckeeper][1] is a package to keep track of changes in the /etc folder, that's where the configuration is supposed to be. So you get a trackrecord of changes in configuration -a must have-, most definitely when you run yum-cron nightly. Its also a great way to document why some things have been changed.

Like most cool tools, after doing it, you totally forget how you got it working. So here I share how I did it and plan how to use it. I picked git, as this is the default way, and git is hip these days.

**On the server you want keep in revision**

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># like most things in life, package is in epel
yum install epel-release

# I choice you, git!
yum install etckeeper git

# go etc
cd /etc

# lets init
etckeeper init

# first commit
etckeeper commit "init our configuration server"</pre>

**Remote**

While strictly speaking not necessary I like to have my configuration saved somewhere else, when git FUBAR's or server won't boot, at-least we can look how the configuration was (or was not) changed.

on our target server : (I try to create a password less login, perhaps other methods are available)

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># I want the configuration on a remote server (central in my case)
# note : security wise this might not be 100%

# create a key
ssh-keygen

# copy the key 
ssh-copy-id -i etckeeper@sysadmin

# or alternative
cat .ssh/id_rsa.pub | ssh etckeeper@sysadmin 'cat &gt;&gt; /home/etckeeper/.ssh/authorized_keys'</pre>

on the remote server :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># adduser and set pasword for first time login
adduser etckeeper
passwd etckeeper

# create git
su etckeeper
git init --bare /opt/etckeeper/public.git
</pre>

and finally on the target server add the remote : (adapt as needed)

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">git remote add origin etckeeper@sysadmin:/opt/etckeeper/public.git</pre>

and change the configuration :

<code class="EnlighterJSRAW" data-enlighter-language="null">nano +43 /etc/etckeeper/etckeeper.conf</code>

change

<code class="EnlighterJSRAW" data-enlighter-language="null">PUSH_REMOTE=""</code>

to

<code class="EnlighterJSRAW" data-enlighter-language="null">PUSH_REMOTE="origin"</code>

**Manually record changes**

Changing something  in /etc ? A good idea to tell your colleagues why (or the future you).

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false" data-enlighter-language="shell">etckeeper commit "I added this ip to /etc/hosts cause I'm to lazy to type a ip."</pre>

**Auto changes to /etc**

Defaults will catch those ! Yum, yum-cron are caught by a plugin. I am not sure about rpm, but etckeeper will autocommit all changes it finds!

**What changed ?**

Since we use git, most git commands work (git status, git log). So its as easy as : <code class="EnlighterJSRAW" data-enlighter-language="null">cd /etc && git log</code>  or for short <code class="EnlighterJSRAW" data-enlighter-language="null">cd /etc && git log --pretty=oneline</code>

**Pulling back changes **

I have not yet pulled back from the repo, but this should work :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">etckeeper vcs checkout [HASH]</pre>

if you only need one file :

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">etckeeper vcs checkout [HASH] [FILE]</pre>

Useful sources :

  * [site of developer][1]
  * [blogpost with commands][2]
  * [a tutorial by digitalocean][3]
  * [another blogpost with setup][4]

 [1]: http://etckeeper.branchable.com
 [2]: https://coelhorjc.wordpress.com/2014/10/27/howto-version-control-etc-in-linuux-using-etckeeper/
 [3]: https://www.digitalocean.com/community/tutorials/how-to-manage-etc-with-version-control-using-etckeeper-on-centos-7
 [4]: https://blog.tinned-software.net/keep-track-of-linux-configuration-changes-with-etckeeper/