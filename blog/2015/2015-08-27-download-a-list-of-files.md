---
title: Download a list of files
author: svennd

date: 2015-08-27T09:35:26+00:00
url: /download-a-list-of-files/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Downloading ~150 links in a text file, sounds like a horror show, right ? Not really ! The [wget][1] application in any linux distro will help you out.

<pre class="EnlighterJSRAW" data-enlighter-linenumbers="false">wget -i file_with_downloads</pre>

Since my links where coming from one source, wget told me it was  "reusing the connection" ([keep-alive][2] ?) After some time however the server on the other side decided I had downloaded more then enough and killed some of the connections, so some files where not downloaded. Now the problem even got worse, since I had to check if wget had downloaded them or not. There might be an easier solution, but I took my best bash from the closet and came up with :

<pre class="EnlighterJSRAW">while read p; do
  FILE=`echo $p | cut -c44-100`
  if [ -f $FILE ];
  then
    echo "echo OK"
  else
    echo "downloading $FILE"
    wget $p &
  fi;
done &lt;download_list</pre>

This started some 10 wgets simultaneously, which is not all that great, but it worked. Probably some better ways exists to tackle this problem, but if it works it works! 🙂

happy downloading.

_notice : the cut -c44-100 actually cuts off the base url, this will be different from what you download. (unless your downloading the same files, highly unlikely)_

 [1]: http://www.gnu.org/software/wget/
 [2]: http://httpd.apache.org/docs/2.2/mod/core.html#keepalive