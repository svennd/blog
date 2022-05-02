---
title: creating random amount of directory’s with random files in it
author: svennd

date: 2016-06-15T14:08:01+00:00
url: /creating-random-amount-of-directorys-with-random-files-in-it/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux

---
One of the reasons I love Linux is the fact you can combine a few commands and do the most boring jobs in seconds, way better then manual work could have; An example ? I wanted to have a "random" file structure with not empty files. (so files with content) So I created 100 directory's and added 10 files to each directory with random content and increasing size.  Sounds like allot of work right ?

<pre>for a in {1..100}; 
do  
  mkdir dir$a/; 
  cd dir$a; 
  
  for i in {1..10}; 
  do 
    dd if=/dev/urandom of=file_$i.log bs=1M count=$i; 
  done; 
  cd ..; 
  
done</pre>

Bam ! Although full disclaimer, bash should take the credit here ! Thx bash <3 <3 <3

&nbsp;