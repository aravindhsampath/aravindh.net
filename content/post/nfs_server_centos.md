---
title: "NFS server based on CentOS 7"
date: 2018-08-07T12:12:29+02:00
draft: false
featured_image: "/images/NFS_server.jpg"
description: "A how-to guide for configuring an NFS server to share binaries for a HPC cluster. Optimized for performance for a read-only scenario."
toc: true
tags: ["NFS", "performance", "HPC", "sysadmin", "CentOS"]
---
<style>
p {text-indent: 8%;}
</style>
### NFS

**N**etwork **F**ile **S**ystem(NFS) is a widely used distributed filesystem that works reasonably well in most scenarios where you must share a filesytem across several computers. It can use UDP or TCP underneath for transport, and is ofcourse very much configurable to get the best out of it for specific usecases. 

At our research cluster, we use NFS for making user's home directories and research datasets available on all compute nodes. For such a serious usecase, the NFS server needs to be sophisticated enough with caches at several layers to stand up for the performance and durability needs of the workload. So, we rely on an enterprise grade **N**etwork **A**ttached **S**torage system - Isilon for an optimized NFS server.

### Context

What I'm dealing with today, is to share a filesystem with software binaries across all compute nodes in the cluster in a read-only manner. Basically, I will install a software package on one server, and copy the binaries to a location, and voila! it is ready for use by all compute nodes instantly. 
<div class="center mw6-ns">
<img class="pt0 w-100 center" src="/images/read-only-nfs.png" alt="Read only mounts of NFS"></img>
</div>

My goal is to setup in such a way that clients mount this filesystem in read-only mode, and cache it heavily for local use. I, as an administrator will have a way to purge this client-side cache in an automated way when I update the binaries on the NFS server. 

### Server-side setup

As a good first step, update the server and grab the latest NFS utilities.
```bash
yum -y update
yum -y install nfs-utils
```
Add our domain name to idmapd.
```bash
echo "Domain = nice.domain.name" >> /etc/idmapd.conf
```
I want to export /opt on the NFS server to be available for all compute nodes in the cluster. 
```bash
echo "/opt 172.16.0.0/17(ro)" >> /etc/exports
```
To learn more about export options, check the man page..
```bash
man 5 exports
```
Let's tell systemd to start our NFS server and start it at boot everytime.
```bash
systemctl start rpcbind nfs-server
systemctl enable rpcbind nfs-server
```
Add permissions through the firewall so that our clients can talk to this server.
```bash
firewall-cmd --add-service=nfs --permanent 
firewall-cmd --reload
```
Let's add a test file to later verify it on the client.
```bash
echo "Success if you see this" > /opt/testfile
```
#### Performance notes:
<div class="dt center pb1">
  <div class="db dtc-ns v-mid-ns">
    <img src="/images/threads.jpeg" alt="NFS threads" class="w-100 mw7 w5-ns" />
  </div>
  <div class="db dtc-ns v-mid ph2 pr0-ns pl3 pl4-ns">
    <p class="lh-copy">
      Let's talk threads. If you're going for maximum utilisation of the server resources for this NFS server, you should tell NFS to start atleast as many daemon threads as many CPU cores available in the system. In my case, my server has 8 cores. The default # of threads with NFS on Centos 7 is 8. However, I expect this NFS server to concurrently serve about 100 compute nodes when they use software binaries from /opt. So, I could use the extra bit of concurrency. 
    </p>
  </div>
</div> 
It can be checked at 
```bash
grep 'RPCNFSDCOUNT=' /etc/sysconfig/nfs
RPCNFSDCOUNT=16
```
I modified it to 16 to have 16 NFS threads servicing clients. I will increase this number in future if real workloads show a need for it. 

There are other knobs to turn on both server and client to adapt to the kind of workload that is expected, but those are out of scope of this post. 
If you're running a rw workload over NFS and have a lot of time to spare to understand NFS caching and squeezing every last bit of performance out of your setup, the following references would help..

1. https://helpful.knobs-dials.com/index.php/NFS_notes
2. https://docstore.mik.ua/orelly/networking_2ndEd/nfs/ch07_04.htm
3. https://www.avidandrew.com/understanding-nfs-caching.html
4. https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-nfs
5. http://www.admin-magazine.com/HPC/Articles/Useful-NFS-Options-for-Tuning-and-Management

In my case, since my only focus is on servicing reads. I optimized my NFS server with bigger system read buffers so that the NFS daemon threads can service reads faster from memory. The following tweaks the configuration in a way that buffer sizes start from 256 KiB and can be increased to 512 KiB upon need. 
```bash
echo "net.core.rmem_default = 262144" >> /etc/sysctl.conf
echo "net.core.rmem_max = 524288" >> /etc/sysctl.conf
sysctl -p
```
Note: I did this based on my experience from several workloads. Test these kind of knobs with your workloads before turning knobs.

### Client-side setup
As a good first step, update the system and grab the latest NFS utilities.
```bash
yum -y update
yum -y install nfs-utils
```
Add our domain name to idmapd.
```bash
echo "Domain = nice.domain.name" >> /etc/idmapd.conf
```
Enable rpcbind to let the NFS client to talk to the NFS server,
```bash
systemctl start rpcbind 
systemctl enable rpcbind
```
Mount the filesystem for testing by
```bash
mount -t nfs -o 'ro' nice.domain.name:/opt /opt
```
Now, let's see if we are able to read the test file we created on the server.
```bash
> cat /opt/testfile 
Success if you see this
```
Great, things work as we expected. Let's make it permanent and happen at boot time.
In simple environments, you can add an entry to /etc/fstab. 
```bash
echo "nice.domain.name:/opt  /opt                   nfs     defaults        0 0" >> /etc/fstab
```
However, I make use of autofs in my environment. In my specific case, the file /etc/autofs.centos tells the kernel where the mount locations are. The following does it for me.
```bash
echo "/opt                            -noatime,nodiratime,ro opt.ghpc.au.dk:/opt" >> /etc/autofs.centos
systemctl reload autofs.service
```
Let's see what is mounted..
```bash
> cat /proc/mounts|grep opt
/etc/auto.centos.ghpc /opt autofs rw,relatime,fd=19,pgrp=26422,timeout=300,minproto=5,maxproto=5,direct,pipe_ino=1272693 0 0

opt.ghpc.au.dk:/opt /opt nfs4 ro,noatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,port=0,timeo=600,retrans=2,sec=sys,clientaddr=172.16.2.30,local_lock=none,addr=172.16.2.37 0 0
```
As we can see,
Our mount requested has negotiated NFS V4.1 with the server, set itself up as read-only, and uses the rest as defaults. 
When running amongst fairly recent Linux kernels, it is better to go with NFS 4.1 for performance.
For the curious, here are a couple of studies about NFSv4.1 performance

1. [Paper - An Evaluation of NFSv4.1](https://www.fsl.cs.sunysb.edu/docs/nfs4perf/nfs4perf-sigm15.pdf)
2. [Paper - NFSv4.1 Performance under a microscope](https://www.fsl.cs.sunysb.edu/docs/nfs4perf/nfs4perf-microscope.pdf)

However, it is not always true that NFSv4.1 is faster than NFSv3. Some enterprise vendors' implementation of the NFSv4.1 protocol are still immature, and they would recommend you to stay with NFSv3. Obey their recommendations unless you want a huge performance hit. 

### Conclusion
Without any surprises, we were able to setup a simple NFS client & server. The client setup can be replicated in many other compute nodes to share the file system. 

