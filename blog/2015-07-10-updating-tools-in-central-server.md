---
title: Flawless updating of tools in a central user server
author: svennd

date: 2015-07-10T19:13:35+00:00
url: /updating-tools-in-central-server/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - bioinf
  - SysAdmin
tags:
  - samtools
  - update

---
Handy toolboxes such as [SAMtools](http://samtools.sourceforge.net" target="_blank" rel="noopener noreferrer) are getting updated, most of the time they become more useful every iteration. As sysadmin ad hoc, researchers expect to use the meanest, leanest and most cutting edge tools to do there research (unlike you're overpriced [<em>Eye</em>phone](http://futurama.wikia.com/wiki/EyePhone" target="_blank" rel="noopener noreferrer)). However some people rely on -now- deprecated commands in "automated" scripts. Rewriting those scripts is hard (as in allot of work), learning people to read man pages/_-help_ is close to rocket science. So most of the time we end up not updating those much used tools  (or would we be just lazy?). So I came up with this method to have multiple versions installed at the same time, it is probably not the best way. But like this bad boy proves, if it fits, it sits.

<img loading="lazy" class="alignnone wp-image-932 size-full" src="/img//2015/07/29731904-1.jpg" width="150" height="150" srcset="/img/2015/07/29731904-1.jpg 150w, /img/2015/07/29731904-1-1x1.jpg 1w" sizes="(max-width: 150px) 100vw, 150px" /> 

<!--more-->\# first we install the tool (this is different for every tool)

<pre>wget https://github.com/samtools/samtools/releases/download/1.2/samtools-1.2.tar.bz2
tar jxf samtools-1.2.tar.bz2
cd samtools-1.2
make</pre>

\# **don't** do : this will overwrite existing linked version.

<pre>make install</pre>

\# now copy the samtools to the binary's

<pre>cp samtools /usr/local/bin/samtools1.2</pre>

Now the user can just call

<pre>samtools1.2</pre>

for the fancy new version; while the old version can be called upon using :

<pre>samtools</pre>

The real GNU/Linux hackers obviously don't want to type the three extra characters.  Can change there $PATH variable in the profile, forcing your own binary's to be executed before system wide binary's. To do this permanently a user can :

\# open bash profile using :  
nano +10 ~/.bash_profile  
\# replace this line :

<pre>PATH=$PATH:$HOME/bin</pre>

\# with

<pre>PATH=$HOME/bin:$PATH</pre>

\# press ctrl+x (to close)  
\# press Y (to save)  
\# press enter (to agree to overwrite the existing file)

\# create the bin directory in your home

<pre>mkdir ~/bin
cd ~/bin</pre>

Now that you created a location and told linux to use your own binary’s, lets link them. (you could also self-compile and put them here now)  
\# create the link to samtools1.2

<pre>ln -s /usr/local/bin/samtools1.2 samtools</pre>

If you have a better way, let me know ! 🙂