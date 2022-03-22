---
title: Create system disk + raid 0 over 2 disks with rocks 7 cluster
author: svennd

date: 2018-04-12T14:21:02+00:00
url: /create-system-disk-raid-0-over-2-disks-with-rocks-7-cluster/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin
tags:
  - filesystem
  - rocks

---
I have been playing with setting up a Rocks 7 cluster, our compute nodes have 3 disk slots. One should be used for the system, and the other two can be used in a RAID 0, which provides faster read/writes and **no redundancy or safety.** But for a cluster where data is never "stored" that is fine.

##### Configuration

To get a custom partition, one needs to copy a file _custom-partition.xml _to the site-profile directory :

<pre>cp /export/rocks/install/rocks-dist/x86_64/build/nodes/custom-partition.xml /export/rocks/install/site-profiles/7.0/nodes/replace-custom-partition.xml</pre>

Then the content needs to be adapted. This protocol is based on [Red Hat kickstart][1]. I used this config :

<pre>&lt;?xml version="1.0" standalone="no"?&gt;
&lt;kickstart roll="base"&gt;
&lt;!-- Custom Partitioning Node --&gt;
&lt;pre&gt;
&lt;!-- clean 3 disks, 50gb root, 10gb swap, 10gb /var, raid0 /state/partition1  --&gt;

echo "clearpart --all --initlabel --drives=sda,sdb,sdc
part / --size 50000 --ondisk sda
part swap --size 20000 --ondisk sda
part /var --size 10000 --ondisk sda
part raid.00 --size 1 --grow --ondisk sdb
part raid.01 --size 1 --grow --ondisk sdc
raid /tmp --level=0 --device=md0 raid.00 raid.01" &gt; /tmp/user_partition_info

&lt;/pre&gt;
&lt;/kickstart&gt;</pre>

Beside the xml tags, important are :

<code class="EnlighterJSRAW" data-enlighter-language="null">clearpart --all --initlabel --drives=sda,sdb,sdc</code>

This initializes the disks, drives should be available under /dev/sd*

<code class="EnlighterJSRAW" data-enlighter-language="null">part / --size 50000 --ondisk sda</code>

For the root I take a 50GB partition, on the primary disk (system disk)

<code class="EnlighterJSRAW" data-enlighter-language="null">part raid.00 --size 1 --grow --ondisk sdb</code>

This is where the raid disk get configured, I set it to the maximal size of the disk, be sure to change the -ondisk parameter.

<code class="EnlighterJSRAW" data-enlighter-language="null">raid /tmp --level=0 --device=md0 raid.00 raid.01</code>

Here you define the raid, if you want to be allot more safer, level=1 would also help with read speed, although at the cost of 50% storage.

<code class="EnlighterJSRAW" data-enlighter-language="null">&gt; /tmp/user_partition_info</code>

important to note is, that ">" should be encoded otherwise it won't work. As described in the [documentation][2].

##### Force reinstall

If like me your cluster is already installed, you are going to want to force this setup on the nodes. Anyways, even if you still need to install the nodes you need to push this configuration into your active distribution you are sending over PXE. This can be done :

<pre>cd /export/rocks/install
rocks create distro</pre>

In case you already installed some nodes, you need to remove _.rocks-release_ file from each first partition. This can be done using the provided script : (/opt/rocks/nukeit.sh)

<pre>for file in $(mount | awk '{print $3}')
do
  if [ -f $file/.rocks-release ]
  then
    rm -f $file/.rocks-release
  fi
done</pre>

Then run it : (for compute-0-0!)

<pre>ssh compute-0-0 'sh /opt/rocks/nukeit.sh'</pre>

Once that is done, you can set the database to remove partition table and reinstall upon next boot.

<pre>rocks remove host partition compute-0-0
rocks set host boot action=install compute-0-0</pre>

To finish off with a reboot, which in turn will initiate the reinstall.

<pre>ssh compute-0-0 'reboot -h now'</pre>

Perhaps a forced kickstart might be required :

<pre>/boot/kickstart/cluster-kickstart</pre>

Happy computing !

&nbsp;

 [1]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-kickstart2-options
 [2]: http://central-7-0-x86-64.rocksclusters.org/roll-documentation/base/7.0/customization-partitioning.html#CUSTOMIZATION-MODIFYING-PARTITIONING-DEFAULT