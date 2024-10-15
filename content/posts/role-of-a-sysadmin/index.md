+++
author = "Aravindh Sampathkumar"
categories = ["Computers"]
date = 2024-01-15T12:00:00Z
summary = "From the trenches of High Performance Computing clusters"
tags = ["Sysadmin", "SRE", "performance"]
title = "Role of a sysadmin"
type = "post"
images = ["img/lean_fast.jpg"]
draft = true
+++

## Duties
A sysadmin is responsible for upkeep, configuration, and reliable operation of systems and services.
![duties](duties.png)

## Accidental sysadmin
I was living in Raleigh, North Carolina, working as a Performance Engineer when I decided to move to Denmark to be with my then girlfriend, now wife, and start a family. Moving to another continent entails a lot of change and looking for a job is no different. I learned soon that finding a job as a Performance Engineer in Europe is not as straightforward as it would be in the US. I instead ventured on a job search based on the skills I have. Voila, I found that I could be a starter sysadmin - I have administered systems before, and turns out there is a lot of commonality between "understanding the system enough to engineer its performance" and "administering a system". 

After some Skype interviews, and arduous work permit paper work, here I am in Aarhus, Denmark.

![Aarhus](aarhus.png)

## Orientation
Orientation or transition is very important for a sysadmin is very important, particularly so for someone who is in those shoes anew. Here are some observations I made on the first day in the job :

-   My predecessor had left the job a few months before my arrival
    
-   A barn is re-purposed as a capable data center
    
-   There is no documentation whatsoever about inventory or purpose of the machines
    
-   The HPC batch scheduler system currently in use (PBS) is causing a lot of pain
    
-   All of my colleagues are bioinformaticians/researchers except one who had been a sysadmin early in his career
    
-   All of my colleagues are nice to me and are rooting for me to do a great job
    
-   I have a lot of room to do good and learn
    

So, I buckled up and started the ride.

![datacenter](barn.png)
![wires](wires.png)

## Reconnaissance
Before I began, I needed to explore and understand what makes up this cluster and what I should be dealing with. Given the lack of documentation, I had to do the reconnaissance on my own. Here is how I went about it..

**nmap** - scan the network(s) to determine which hosts are online, what services they offer(web, database, DNS, firewall etc), which operating system versions are they at, ports open in them and more.

This resulted in an Excel document that I treated as an inventory from then on. 

Next up, **DNS records**. Once I found out which servers are providing name resolution, I looked at their records to get a list of all services that could be in use by others. My Excel doc was made slightly better and richer with information.

Next, searched for a centralised syslog somewhere, and realised there isnt one.

Moving on, **users and groups**. Another result of the network probe was the NIS server that was acting as the identity provider. So, logged in there, and grabbed the list of users and groups. With the help of senior colleagues and some common sense poking around, I made notes of who uses the system, and the purpose of the groups. I had never worked with NIS before, and made note of it being a pain point.

**Cron jobs** : One of the conversations with users of the systems pointed to some cron jobs that happen in the cluster. So, I listed crontab on all machines and obtained information about the cron jobs that were to be cared for. 

An advantage of a controlled private cluster such as this one is that you can probe the entrypoint and understand which users can get how far. Since the primary interface to the clsuter was via SSH, I probed the `sshd_config` files to see who has access to go where.

##

