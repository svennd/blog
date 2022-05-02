---
title: 'fatal error: ffi.h: No such file or directory'
author: svennd

date: 2017-02-04T16:57:58+00:00
url: /fatal-error-ffi-h-no-such-file-or-directory/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
Missing library's are painful, this one I hit upon while compiling some python dependency's.

<pre>gcc -pthread -fno-strict-aliasing -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DUSE__THREAD -I/usr/include/ffi -I/usr/include/libffi -I/usr/include/python2.7 -c c/_cffi_backend.c -o build/temp.linux-x86_64-2.7/c/_cffi_backend.o
    c/_cffi_backend.c:15:17: fatal error: ffi.h: No such file or directory
     #include &lt;ffi.h&gt;
                     ^
    compilation terminated.
    error: command 'gcc' failed with exit status 1

    ----------------------------------------
Command "/usr/bin/python2 -u -c "import setuptools, tokenize;__file__='/tmp/pip-build-EHadKY/cffi/setup.py';exec(compile(getattr(tokenize, 'open', open)(__file__).read().replace('\r\n', '\n'), __file__, 'exec'))" install --record /tmp/pip-5wnmzT-record/install-record.txt --single-version-externally-managed --compile" failed with error code 1 in /tmp/pip-build-EHadKY/cffi/</pre>

for Centos 6/7 (and all other redhat related distro's)

<pre>yum install libffi-devel</pre>

for Debian brothers :

<pre>apt-get install libffi-dev</pre>

&nbsp;