---
title: Webserver configuration for phpBB server on VPS
author: svennd

date: 2015-12-30T11:17:39+00:00
url: /webserver-configuration-for-phpbb-server-on-vps/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
This is a project I have been working on for some time, I manage a phpBB 3.1 forum that is pulling some traffic -that is great- the problem : server is not following. I found the server (VPS) dead already a few times in the morning showing OOM errors, killing of MySQL or Httpd. Resulting in the forum being totally unreachable, bad for the site, bad for google results, bad for my rep. even worse for my honor as _ad interim_ sysadmin.

Solving this problem can be very easy : throw in allot of cash and upgrade to larger VPS packet or dedicated server. Before I take that expensive path, I want to be sure I have squeezed everything out of the current hardware and configuration. I "know" four webservers : Apache, Nginx, lighthttpd and ISS. However, the last one is the windows one, I wanne stay far away from it (blind discrimination). **Lighthttpd** I know from memory, but I haven't looked it up, and I -probably wrongly- think it focuses on dropping less-used features and being a stable simple webserver, hence the name, simple and stable is not my ballpark for this. **Apache** I have setup a few times for small websites, while I would not call myself experienced I am certain that I could fix most common mistakes. On **Nginx** I have read the story's that it <span style="text-decoration: underline;">could be</span> setup to be multiple factors faster then Apache, due to the way it works, however on most recommendations, I found that people gave the advice to use Apache, cause its simpler to setup/maintain. For the purpose of squeezing everything from setup I will go with Apache since I'm a bit experience with it and Nginx, as its large selling point is the increase in speed at a lower usage in resources.

**Current stack**

Currently I am running vanilla Apache. The first thing to check if something is going slow is seeing what is making it go slow, the so called bottleneck. The stack is Centos 6.7, Apache 2.2, PHP 5.6, MySQL 5.1 and phpBB 3.1. Those in itself can be updated to the latest version, though updates rarely speed up application hugely unless a bug is known. Centos has not the bleeding edge new software and that makes them a good bet on being stable. They also update the software packages so that those huge bugs would be fixed regardless of the version.  So while updating could make some difference, I doubt that is where I am going to find the golden egg.  phpBB can't be replaced in this case. (at-least that is not the preferred route)

**Testing setup**

There are some good tools online to test your application under load, sadly all the nice ones aren't free. I don't wanne go and spend a few hundred bucks on testing, that is simply not a option.  Ideally a load tester for phpBB should register user, post topics, read topics, edit topics, ... most users don't reload 100 times a same page ... Still that is the only easy test I found. The tool[ Apache benchmark](https://httpd.apache.org/docs/2.2/programs/ab.html" target="_blank) "simply" makes concurrent connections to the webserver. There are some alternatives, such as [httperf][1], [siege][2], [jMeter][3], [Tsung][4], [gor][5], [gatling][6], ... all these tools are probably better then ab, but sadly take allot of configuration and work to get started with, I am going to try at-least a few of them but for now, AB has proven to be able to shoot the arrow straight in at my servers 'Achilles'. Since I want to test multiple setups, I created a small bash script that will do a serie of ab tests.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">SERVER="http://server/"

# ab run function will pull ab test
function ab_run { 
  # report
  echo "run $1 / $2" 
  # ab -n requests -c concurrent_users url
  ab -n $1 -c $2 $3 &gt; result/ab_$2_$1 2&gt;/dev/null;
  
  # let it stabilize
  sleep 15
}

# 1000 requests/10 concurrent_users

ab_run 50 5 $SERVER
ab_run 100 10 $SERVER
ab_run 200 20 $SERVER
ab_run 300 30 $SERVER
ab_run 400 40 $SERVER
ab_run 500 50 $SERVER
echo "ab run completed"</pre>

The scripts writes output to result/ directory, this output I will later reuse. Since I ran this on a VPS with limited resources (2CPU/2GB RAM) I had to scale down to get load that would not crash the server to begin with. I don't want to test long stability, so I took a tenfold of concurrent requests. I let the server stabilize for 15 seconds, to see some more spread in load peaks. 15 seconds is not long enough to drop to zero load, so there is a bit of overlap to be expected. This is a simple test that will put load on the server, so don't run it in production (unless you want customers on the phone). Also note that there is a limit on concurrent request a client machine is able to produce, though these values don't come even close to that number. ([while 50 users sound little, its a good starting point](http://serverfault.com/questions/274253/apache-ab-choosing-number-of-concurrent-connections" target="_blank)) This is not a the best method, but it is a decent starting point in my opinion.

**Setups**

I have tested 7 setups :

  * _Test 1,_ was a '**base line**' test, I pulled static file from a freshly installed LAMP stack. (the forum was installed, just not accessed) This is useful to see if the test in itself is possible to generate load and to see if everything is working. Its also a good way to check the difference between static vs dynamic pages.
  * _Test 2,_ was default Apache setup pulling the **index page of phpBB** forum, I believe this is presentabele enough to other public pages of the forum.  The default value of keep-alive is off in apache, so adding the flag keep-alive won't speed up, in fact keep-alive keeps the connection open for a timeout, but AB won't request any other files (normal users would). But unless I shorten the timeout, I would need to add a large amount of time to the stabilizing. Like this, it is the worst case scenario setup.
  * _Test 3,_ was changing the **file cache** phpBB with **memcache**. Setting up memcache with 1024 connections and 64 mb RAM. While some more RAM in production might be advisable for the ab test, only one page will be requested. In fact lowering the memory (since this only a 1 page test) might give a better test result. (as in, more realistic)
  * _Test 4,_ was setup 3 with the **APC module** enabled for PHP, this would save the PHP bytecode in memory. I found most blogs/fora referring APC as the best/easiest way to go.  It was officially endorsed by PHP to be included in PHP 6, but seemingly the opinion has changed in favor for Zend opcache ... While phpBB can also use APC as cache, I am not sure what is recommended here. APC will work no matter what phpBB decides, while memcache is only used when the applications requests it.
  * _Test 5_, was setup 4 with **MaxClients** set to 20, the idea behind that was, its better to queue clients then the forum is to go dark. (20 concurrent users over 24h would be 1.7 m requests, _not 1.7m visitors_!)
  * _Test 6_ : enough of Apache, lets hit the **Nginx** park. A clean setup. Lets take this as a baseline. php-fpm was used. memcache was kept with the same setup as test 3.
  * _Test 7_ : phpBB has an example in there repo of how to setup nginx for phpBB, I took it over and checked if I missed some low hanging fruit. The most important addition was the explicit configuration of using **gzip**.

Those setup form a basis for me to go into more specific setups. During the test I ran this simple bash script to get values on load/memory.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false">while :
do
  free -m &gt;&gt; memory_data
  uptime &gt;&gt; cpu_load
  
  tail -n 1 cpu_load
  sleep 5
done</pre>

It will print out the load values so you could stop the test if the values get to high. (they did, sorry co-vps'rs)

**Pulling data from results**

I put every result file in a directory load\_($test\_nr) getting the data to something workable are a bunch of hacks. But I share them for the future me.

<pre class="EnlighterJSRAW" data-enlighter-language="shell" data-enlighter-linenumbers="false"># get cpu load 1 min
for i in {1..7}
do
   cat load_$i/cpu_load | cut -c45-49 &gt; compile/cpu$i
done

# get memory useage
for i in {1..7}
do
   sed -n '2,${p;n;n}' load_$i/memory_data &gt; compile/memory$i
done

# get failed requests
for i in {1..7}
do
  cat result_$i/ab_*_* | grep Failed | cut -c 25-40 &gt; compile/failed$i
done
 
# total time
for i in {1..7}
do
  cat result_$i/ab_*_* | grep "Time taken for tests:" | cut -c 25-40 &gt; compile/total_time$i
done

# requests per second
for i in {1..7}
do
  cat result_$i/ab_*_* | grep "Time taken for tests:" | cut -c 25-40 &gt; compile/rps$i
done

# time per request accros all
for i in {1..7}
do
  cat result_$i/ab_*_* | grep "Time per request" | grep "across all concurrent req" | cut -c 25-31 &gt; compile/tpr$i
done</pre>

**Results & Discussion**

_note : I am no expert in this, these test have huge biases and are in no way close to how normal users would interact with your application/board. These values and results are not statistically correct. Don't change stuff you haven't tested. _

_note 2 : While the production server has been running Centos 6.7 I took Centos 7.1__ to test run._

**CPU**

One of the ways to see how good or bad a server is doing is checking out the CPU load, this is a value that tells you how much CPU _power_ has been used over a period (1, 5, 15 minutes). I set out the total run time against the load out. The total run time are the count(values)*5s since I had no easy way of setting it out I did not bother, so no exact values there.

First off, these loads are crazy. (this is the 1 minute load) since this is a 2 CPU VPS, a maximum load of 2 should be the target. (roughly two virtual CPU maxed out) I left out the first test, as the maximum CPU load was 0,03. That was to be expected, requesting static files with no processing on server side should not generate load on CPU.  The clean version (test 2) has a maximum load of 39,3 and ran the longest, while using memcache as forum cache (test 3) reduced the time it required to finish, the load is approximately the same. (max test 2 : 39,8). The largest win time wise, is using APC (test 4), it speeds up PHP execution hugely, also the CPU's are slightly less stressed,  load maximum : 27,4 probably cause the CPU doesn't have to make bytecode from phpBB's code. In real life examples I don't think the effect will be this huge under those load conditions, cause APC can't predict, it can just keep bytecode of most used files. Since I still had a load ~20 fold the amount I can be sure to claim (VPS!) I needed to be sure to pull down the CPU usage, one way I found was to lower the MaxClients this will queue users that hit the server over the limit and as such make sure the server remains stable, by default apache comes with 256, this clearly is too much for dynamic PHP pages. I took arbitrary value 20 to see the effect on CPU. The result was astonishing, the CPU load kept below 5,6. Which is still about twice too high load, but its a good indication of what I can do with this parameter. Lowering it even more would put to much visitors in queue and result in very slow experience, to large value would result in to high CPU and eventually some service would give out and break. Perhaps this parameter should be combined with keep-alive. (more testing needed)

The results with Nginx where not significantly different from Apache, the load was 24,64 without gzip and with 25,33. The load was a bit lower then Apache : 27,4 (memcache was in both used and APC was active on both). This was somehow expected, while the technology of how Apache and Nginx handle the requests differ greatly, the most powerful feature, server side caching where on both Apache and Nginx omitted. I also have close to no experience with Nginx and as such purely on these results Nginx is not a clear winner. I am however not certain APC was active on the _php-fpm_ (v : 5.4.16) I will test this later. The reason for not using server side caching, is simple : I hit a single PHP page that can be cached in the test, but in real world scenario can't be cached long. That being said, a [microcache][7] such as is possible in Nginx might be very useful as multiple hits on the same page could be short-live cached and so making 100 hits on a single page could be pretty much be done from a 10 second cache. However the setup of something like that would be rather difficult without good knowledge of Nginx. (I am working on it !) CPU wise some more research is needed to conclude anything. Now most errors where memory wise, out of memory errors. So it might be good to see what exactly happens with the memory during the tests.

**Memory**

Testing memory is not as easy, while there is a tool <code class="EnlighterJSRAW" data-enlighter-language="null">free</code>_ _that reads and parses memory usage, [Linux/GNU is not as straightforward as : you still have memory/you don't have any memory left.][8] So for these values I used the "available" memory:  _free from procps-ng 3.3.10 (version) _:

> Estimation of how much memory is available for starting new applications, without swapping. Unlike the data provided by the cache or free fields, this field takes into account page cache and also that not all reclaimable memory slabs will be reclaimed due to items being in use

**ab results**

The ab results gives me allot of data, but my doubt has become true. The data is not that useful. I parsed : requests per second, total run time and time per request.  I will only show a graph for the last one, as they are all pretty similar.

Showing pretty much what I expected from the CPU usage, APC is the only factor that really gives the server a boost. Making the static serving time come closer.

**Conclusion & Thoughts**

There is no such thing as a bad experiment or bad results, just bad use of the given data. This test was setup to give a raw idea of where to go. As such I have no experience with testing tools or testing a webserver to begin with.

  1. ab is a simple and great to use tool. But aside from hammering the server, the application below is not really tested.
  2. APC or any other tool that caches the output of PHP bytecode has a real effect both on load and speed. I am not surprised there was some voice to include it by default, but since I tried this on Centos 7. I believe it was not yet included as of now.
  3. Memcache seems to have little to no effect, I was a bit surprised in that, but  the test is the problem here. Since phpBB in itself caches on file, the move from file cache to memory cache is only limited.
  4. While this test was for phpBB, the effect on phpBB has not really been tested and further tests are needed to say anything useful, expect for the fact that APC is a real booster, at least in these tests.

While I keep on searching, feel free to comment or give advice!  [All the data shown here][9] can be accessed on google docs.

![29730888](/img//2015/07/29730888-1.png)![29730848](/img//2015/07/29730848-1.png)          ![29729976](/img//2015/07/29729976-1.png)       ![29730272](/img//2015/07/29730272-1.png)

 [1]: https://github.com/httperf/httperf
 [2]: https://www.joedog.org/siege-home/
 [3]: http://jmeter.apache.org
 [4]: http://tsung.erlang-projects.org
 [5]: https://github.com/buger/gor/
 [6]: http://gatling.io/
 [7]: https://www.google.be/search?q=micro+cache+nginx
 [8]: http://www.linuxatemyram.com
 [9]: https://docs.google.com/spreadsheets/d/1MqxLoYfE5vVstIFP18TmRsnrQaW93Gug7Vo40Va9ZtQ/edit?usp=sharing