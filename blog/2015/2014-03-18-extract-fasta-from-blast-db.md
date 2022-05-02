---
title: Extract fasta from blast DB
author: svennd
date: 2014-03-18T09:28:10+00:00
url: /extract-fasta-from-blast-db/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - bioinf

---
This will save google some monthly data from me, so here goes. Dumping a blastDB to fasta format :

<pre>fastacmd -d BLASTDB -D 1 &gt; blast_db_dump.fasta</pre>

(1 for fasta, 2 GI list, 3 accession.version list)

I also found another method, but I don’t have the tool “blastdbcmd”. So maybe you might use :

<pre>blastdbcmd -db my_blast_db -entry all &gt; blast_db_dump.fna</pre>

Also see [this reference][1]. For information about [blastdbcmd, see this reference][2].

 [1]: http://nebc.nox.ac.uk/bioinformatics/docs/fastacmd.html
 [2]: https://www.ncbi.nlm.nih.gov/books/NBK279689/
