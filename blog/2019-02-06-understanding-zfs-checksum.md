---
title: 'Understanding ZFS : Checksum'
author: svennd

date: 2019-02-06T09:43:46+00:00
url: /understanding-zfs-checksum/
thumbnail: /img/2020/05/kevin-mueller-snzT58xkxtE-unsplash-scaled.jpg
categories:
  - Linux
  - SysAdmin
  - ZFS
tags:
  - checksum
  - zfs

---
Ever wondered what kind of checksum ZFS uses to check for bit rot ? Probably not, but it turns out you can change the used algorithm. However like most settings, the defaults are chosen by smart people. So changing it might not be doing you any favors.

As it turns out, the default checksum used is [Fletcher's checksum][1];This algorithm is comparable to [CRC][2] error detection, but outperforms it by nearly 20 times per byte ([source][3]). So it looks as if this is really speedy.

This algorithm can actually use some of the more modern Intel CPU optimizations. ([source][4]) To check if your CPU has these optimizations, check your CPU flags, on most distro's this can be done using :

<pre>cat /proc/cpuinfo | grep flags

or 

lscpu | grep -i flags</pre>

Flags of interest are sse2, ssse3, avx2 and avx512f. I'm well outside the scope of knowledge here; but it seems those optimizations can vary between CPU's and so its not always sure if the latest optimization is the best; so ZFS actually mini-benchmarks just after its kernel module is loaded to determine what algorithm is best. Even nicer, the results are stored in /proc and you can check what your CPU's scores are; You can check it in : <code class="EnlighterJSRAW" data-enlighter-language="null">/proc/spl/kstat/zfs/fletcher_4_bench</code>

In a recent server ([Intel Xeon Silver 4110][5]), avx512f/avx2 is picked :

<pre>cat /proc/spl/kstat/zfs/fletcher_4_bench
0 0 0x01 -1 0 1054412431662536 2941001241716525
implementation   native         byteswap
scalar           3455422459     2778248102
superscalar      4626459244     3440503186
superscalar4     4008342143     3352064854
sse2             7888619803     4445292423
ssse3            7891663628     7030719031
avx2             12054042156    10904840790
avx512f          19645275791    6985259129
fastest          avx512f        avx2</pre>

While on an ancient test machine([Intel Pentium Dual CPU E2220][6]) , superscalar is picked over "newer" flags such as sse2.

<pre>cat /proc/spl/kstat/zfs/fletcher_4_bench
0 0 0x01 -1 0 3970297751 15299241313174195
implementation   native         byteswap
scalar           3690080571     2332985088
superscalar      4357932898     2616994380
superscalar4     3849236054     2487645215
sse2             2809499310     2262775111
ssse3            2809413022     2308094121
fastest          superscalar    superscalar</pre>

Pretty nice work, all done behind the scene in a transparant way.

Back to the stuff we can actually play with, changing the algorithm; It turns out, that one can change this to SHA256, which is required to run deduplication and that's about it; The other alternatives have been deprecated or are not implemented in zfsonlinux, these are fletcher2, SHA512, [skein][7] and [edon-r][8]. You can also disable the checksum for a certain dataset or for an entire pool, but then the question is why even chose for ZFS.

Changing the checksum can be done like most values (as per docs) :

<pre>zfs set checksum=sha256 pool_name/dataset_name
zfs set checksum=fletcher4 pool_name/dataset_name</pre>

So while its really interesting to know what is going on behind the scenes, I doubt many people should play with this unless you know what you are doing. In which case this article is not aimed for you 😉

As always, relevant information, and basically the source of this post can be found on the Github wiki of zfsonlinux, [here][9].

 [1]: https://en.wikipedia.org/wiki/Fletcher%27s_checksum
 [2]: https://en.wikipedia.org/wiki/Cyclic_redundancy_check
 [3]: http://collaboration.cmc.ec.gc.ca/science/rpn/biblio/ddj/Website/articles/DDJ/1992/9205/9205b/9205b.htm
 [4]: https://software.intel.com/en-us/articles/fast-computation-of-fletcher-checksums
 [5]: https://ark.intel.com/products/123547/Intel-Xeon-Silver-4110-Processor-11M-Cache-2-10-GHz-
 [6]: https://ark.intel.com/products/32430/Intel-Pentium-Processor-E2220-1M-Cache-2-40-GHz-800-MHz-FSB-
 [7]: https://en.wikipedia.org/wiki/Skein_(hash_function)
 [8]: https://pdfs.semanticscholar.org/e901/492cbb9d1f8a4365397676da808a9d9415cc.pdf
 [9]: https://github.com/zfsonlinux/zfs/wiki/Checksums