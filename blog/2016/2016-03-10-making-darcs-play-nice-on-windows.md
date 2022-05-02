---
title: Making darcs play nice on windows
author: svennd

date: 2016-03-10T10:57:41+00:00
url: /making-darcs-play-nice-on-windows/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - windows

---
I doubt any living soul is still using darcs on windows, so this will be very useful for myself when I reinstall my computer at some time, or god forbid my _HP Elitebook 8530p_ dies on me. Either way, this will get darcs running on windows, so you can use the repo from a Linux machine.

**Get everything :**

  * Darcs binary, compatible with windows :  (I had to use [Darcs 2.8.1 msi][1])
  * All the putty and friends binary's to get ssh/scp commands working : ([download full zip][2]) Info on these bin's and there checksums [can be found here][3]

**Create the location**

This might not strictly be needed but I put everything in c:/tools/ A directory I am going to add to environment variables. This is equal to $PATH in Linux and makes it easier to call binary's (exe) from console. For windows 10 that's just searching for them in start menu. (its part of configuration) see images how I did it (its in _dutch_ sorry)

![25451944](/img//2015/07/25451944-1.png) 

![25451984](/img//2015/07/25451984-1.png) 

**Copy & rename some bins**

  * copy plink.exe to ssh.exe
  * copy pscp.exe to scp.exe

Perhaps not necessary but I set property's that I trust these files although I downloaded them from the internet.

![25452136](/img//2015/07/25452136-1.png) 

**Generate key**

After that we get to create a keypair, since our server works with key pair and not a password login. For that use puttygen.exe; hit the generate button and move the mouse over the grey area "randomly".  Save both the public and private key. On the server add the top field (in my case it starts with <code class="EnlighterJSRAW" data-enlighter-language="null">ssh-rsa AAAAB3NzaC1</code>) to _nano ~/.ssh/authorized_keys  _of the user that you wanne login with. (the darcs users)

Now we would like to test the key using : pageant.exe, double click the app, in the taskbar and icon will apear, richt click it to add the private key you just generated. Then go to cmd and ssh to the user@server it will ask you if you trust the server (and add it to server cache) hit yes and you should be logged in. (I made a password less key) if you can't login something when wrong, try again.

**Darcs p0wers**

I added darcs to the tools path, you could also add both c:/tools and c:/program files/darcs to environment path, but either way, check the method you prefer by running darcs in cmd, it should show the help screen. Then go the location you want to get the repo going and <del>pull</del> get it from the server!

<pre>darcs get user@server:repo/</pre>

Woohoo another thing I'm great at!

 [1]: http://darcs.net/binaries/windows/
 [2]: https://the.earth.li/~sgtatham/putty/latest/x86/putty.zip
 [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
