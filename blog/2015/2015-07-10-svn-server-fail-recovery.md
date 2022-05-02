---
title: SVN server fail recovery
author: svennd

date: 2015-07-10T20:11:59+00:00
url: /svn-server-fail-recovery/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
tags:
  - recovery
  - svn

---
<img loading="lazy" class="alignnone wp-image-935 size-full" src="/img//2015/07/29732568-1.jpg" width="300" height="199" srcset="/img/2015/07/29732568-1.jpg 300w, /img/2015/07/29732568-1-2x1.jpg 2w" sizes="(max-width: 300px) 100vw, 300px" />

Oke, shoot me, I still love and use subversion (SVN) 🙂  So what happens when my lovely SVN server (my desktop :$ ) breaks down ? All hell breaks lose! ([before you start][1])

Well that’s a bit cheesy, since actually the hard drive still lived and all the checkouts on the servers still are up-to-date and the latest release is pretty much the most important one -for me-. Though how do you fix a SVN once the mother-system gives up … well I found a way, perhaps not the best or cutest way; but hey, you're here to find answers aren’t you ?

First I needed to install svn, (new machine right)

<pre>sudo apt-get install subversion</pre>

after that, I mounted the old disk;

<pre>sudo mkdir /media/data
sudo mount /dev/dbc1 /media/data</pre>

Then you attempt to take a dump; svn dump that is.

<pre>sudo svnadmin dump /media/data/var/www/svn/myreponame &gt; ~/repo.dump</pre>

Then I created a new repo, I have no idea where the best place is to locate a repo, so I chose /var/svnrepo for a change; adapt to your own wishes. (note : don't store it somewhere public obv.)

<pre>sudo mkdir -p /var/svnrepo
sudo chown $USER:$USER /var/svnrepo
svnadmin create yournewrepo</pre>

Once we are here, you should see a directory in /var/svnrepo/yournewrepo so now we can start loading your dump in. (This does sound wrong)

<pre>svnadmin load yournewrepo &lt; ~/repo.dump</pre>

This last process takes quite a time to finish, but perhaps this is my mistake, since at one time in development, I thought huge blocks of raw data to SVN was a good idea. My dump was 5,5 Gb.

Hope it helps you save your precious SVN repo!

&nbsp;

 [1]: https://gigaom.com/2012/09/11/uh-oh-githubs-down/