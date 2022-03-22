---
title: Rocket Chat (0.60.3) on Centos 7
author: svennd

date: 2018-01-09T14:29:56+00:00
url: /rocket-chat-0-60-3-on-centos-7/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - centos
  - rocketchat

---
With the latest stable Rocket Chat (0.60.3) on Centos 7 machine I got this error :

<pre># node main.js
/opt/Rocket.Chat/programs/server/boot.js:50
    const { pause } = require("./debug.js");
          ^

SyntaxError: Unexpected token {
    at exports.runInThisContext (vm.js:53:16)
    at Module._compile (module.js:373:25)
    at Object.Module._extensions..js (module.js:416:10)
    at Module.load (module.js:343:32)
    at Function.Module._load (module.js:300:12)
    at Module.require (module.js:353:17)
    at require (internal/module.js:12:17)
    at Object.&lt;anonymous&gt; (/opt/Rocket.Chat/main.js:4:1)
    at Module._compile (module.js:409:26)
    at Object.Module._extensions..js (module.js:416:10)
</pre>

This can be fixed by installing a few dependency (magic)

<pre>npm install -g inherits n</pre>

Then it seems to be recommended to run node.js 8.9.3 :

<pre>n 8.9.3</pre>

After that I got greeted with a well-known issue on Centos ... the older GLIBC librarys :

<pre>[root@rocket Rocket.Chat]# node main.js
module.js:664
  return process.dlopen(module, path._makeLong(filename));
                 ^

Error: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.20' not found (required by /opt/Rocket.Chat/programs/server/node_modules/fibers/bin/linux-x64-57/fibers.node)
    at Object.Module._extensions..node (module.js:664:18)
    at Module.load (module.js:554:32)
    at tryModuleLoad (module.js:497:12)
    at Function.Module._load (module.js:489:3)
    at Module.require (module.js:579:17)
    at require (internal/module.js:11:18)
    at Object.&lt;anonymous&gt; (/opt/Rocket.Chat/programs/server/node_modules/fibers/fibers.js:24:38)
    at Module._compile (module.js:635:30)
    at Object.Module._extensions..js (module.js:646:10)
    at Module.load (module.js:554:32)</pre>

Luckily in this case there is a quick and easy workaround, install some build apps in case they are missing :

<pre>yum install g++ build-essential</pre>

If these are not available you can try :

<pre>yum install gcc gcc-c++ make openssl-devel</pre>

rebuild node-gyp :

<pre>npm install -g node-gyp
cd /opt/Rocket.Chat/programs/server/node_modules/fibers/
node-gyp rebuild
cp -f build/Release/fibers.node bin/linux-x64-57/fibers.node</pre>

And rocketchat will boot up again 😀 jippie !

<pre>systemctl start rocketchat.service</pre>

Happy rocketing ! 🙂

Thanks to [stally][1]!

 [1]: https://github.com/stalley