---
title: Apache Failed to create shared memory
author: svennd

date: 2018-12-03T15:34:07+00:00
url: /apache-failed-to-create-shared-memory/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - apache

---
Auch, I got the same error as [ma.ttias.be][1] pretty much two years ago. I have no idea why it failed, however the solution seemed to work similar to what he suggested.

<pre>[Mon Dec 03 15:07:55.871866 2018] [auth_digest:notice] [pid 3352] AH01757: generating secret for digest authentication ...
[Mon Dec 03 15:07:55.871892 2018] [auth_digest:error] [pid 3352] (2)No such file or directory: AH01762: Failed to create shared memory segment on file /run/httpd/authdigest_shm.3352
[Mon Dec 03 15:07:55.871897 2018] [auth_digest:error] [pid 3352] (2)No such file or directory: AH01760: failed to initialize shm - all nonce-count checking, one-time nonces, and MD5-sess algorithm disabled
[Mon Dec 03 15:07:55.871901 2018] [:emerg] [pid 3352] AH00020: Configuration Failed, exiting</pre>

The directory /run/httpd is gone. So recreate it and give correct permissions; This in my case (on Centos 7X was apache instead of httpd)

<pre>mkdir /run/httpd
chown root.apache /run/httpd
chmod 0710 /run/httpd</pre>

Another solution, sadly the mystery is not solved.

 [1]: https://ma.ttias.be/apache-2-4-ah01762-ah01760-failed-to-initialize-shm-shared-memory-segment/