---
title: Rocks Grid Engine error states
author: svennd

date: 2017-06-27T13:58:17+00:00
url: /rocks-grid-engine-error-states/
thumbnail: /img/2021/03/simon-stratford-_ILkd7aVqAM-unsplash.jpg
categories:
  - Linux
  - SysAdmin

---
I have not found easy explanation on what kind of error states are in Rocks, Grid Engine, so I am collecting them here as I find them.

##### Show states

First let's find the overview of the nodes; this can be done using qstaf -f

<pre>qstat -f</pre>

Result should be something like :

<pre># qstat -f
queuename                      qtype resv/used/tot. load_avg arch          states
----------------------------------------------------------------
all.q@compute-0-0.local        BIP   0/0/24         0.00     linux-x64 
----------------------------------------------------------------
all.q@compute-0-1.local        BIP   0/0/24         0.00     linux-x64
----------------------------------------------------------------
all.q@compute-0-2.local        BIP   0/0/24         0.02     linux-x64
----------------------------------------------------------------
all.q@compute-0-3.local        BIP   0/0/24         0.00     linux-x64 
----------------------------------------------------------------
all.q@compute-0-4.local        BIP   0/0/24         0.00     linux-x64
----------------------------------------------------------------
all.q@compute-0-5.local        BIP   0/0/24         0.00     linux-x64    
----------------------------------------------------------------
</pre>

##### Error state : E

E stands for (hard) error, which means something bad is going on. The result is a decision by the headnode to not use this node anymore until manual intervention. This happens, to make sure there is not a job sinkhole created.

Example :

<pre>qstat -f
queuename                      qtype resv/used/tot. load_avg arch          states
---------------------------------------------------------------
all.q@compute-0-0.local        BIP   0/0/24         0.01     linux-x64     E
---------------------------------------------------------------
all.q@compute-0-1.local        BIP   0/0/24         0.00     linux-x64
---------------------------------------------------------------
all.q@compute-0-2.local        BIP   0/0/24         0.02     linux-x64
---------------------------------------------------------------
all.q@compute-0-3.local        BIP   0/0/24         0.00     linux-x64     E
---------------------------------------------------------------
all.q@compute-0-4.local        BIP   0/0/24         0.00     linux-x64
---------------------------------------------------------------
all.q@compute-0-5.local        BIP   0/0/24         0.00     linux-x64     E
---------------------------------------------------------------</pre>

The error states here where probably due to a full root disk on these nodes. There is a tool for finding out which jobs failed to find out what was happening at the time (-explain E)

<pre>qstat -f -explain E</pre>

<pre>queuename                      qtype resv/used/tot. load_avg arch          states
-------------------------------------------------------------
all.q@compute-0-0.local        BIP   0/0/24         0.00     linux-x64     E
        queue all.q marked QERROR as result of job 542032's failure at host compute-0-0.local
        queue all.q marked QERROR as result of job 542033's failure at host compute-0-0.local
        queue all.q marked QERROR as result of job 542036's failure at host compute-0-0.local</pre>

What is more important, is the fact that Error state will survive a reboot. So we should clean it up if the underlying issue has been resolved : (this will clear all errors)

<pre>qmod -c '*'</pre>

##### Disabled state : d

_d _means the node has been disabled, this normally should not happen automatically. We can disable a node from getting anymore jobs, but the running jobs will continue to run.

You can disable a node from further jobs using the _qmod_ command.

<pre>qmod -d all.q@compute-0-5.local</pre>

You can re-enable a node again using

<pre>qmod -e all.q@compute-0-5.local
</pre>

Example :

<pre>[root@server ~]# qmod -d all.q@compute-0-5.local
root@server.local changed state of "all.q@compute-0-5.local" (disabled)
[root@server ~]# qmod -e all.q@compute-0-5.local
root@server.local changed state of "all.q@compute-0-5.local" (enabled)
</pre>

&nbsp;

##### au : Alarm, Unreachable

The state _au_, _u_ means unreachable this happens when _sge_execd _on the node does not respond to the _sge_qmaster_ on the headnode_ _within a configured timeout window. The a state is alarm, this will happen when the node does not report the load, in which case a load of 99.99 is assumed. This results in the scheduler to not assign more work to the node. The _au_ state can happen when a NFS server is being hammered and the complete node is waiting for the "slow" network disk.  (when hard mounted nfs) This state can resolve itself if the problem gets resolved.

<pre>[root@server ~]# qstat -f
queuename                      qtype resv/used/tot. load_avg arch          states
---------------------------------------------------------------------------------
all.q@compute-0-0.local        BIP   0/0/24         0.00     linux-x64     d
---------------------------------------------------------------------------------
all.q@compute-0-1.local        BIP   0/3/24         3.25     linux-x64     d
---------------------------------------------------------------------------------
all.q@compute-0-2.local        BIP   0/6/24         6.32     linux-x64     d
---------------------------------------------------------------------------------
all.q@compute-0-3.local        BIP   0/0/24         0.00     linux-x64     dE
---------------------------------------------------------------------------------
all.q@compute-0-4.local        BIP   0/5/24         5.28     linux-x64     d
---------------------------------------------------------------------------------
all.q@compute-0-5.local        BIP   0/0/24         0.00     linux-x64     dE
---------------------------------------------------------------------------------
all.q@compute-0-7.local        BIP   0/0/24         -NA-     linux-x64     adu
</pre>

Useful link :

  * Grid Engine Troubleshooting (pdf) (dead :( )
