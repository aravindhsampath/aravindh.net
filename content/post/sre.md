---
title: "A small scale approach to Site Reliability Engineering(SRE)"
date: 2020-04-18T17:12:49+02:00
draft: true
featured_image: "/images/reliability.png"
description: "My practical notes on SRE when you are not of Google's scale."
toc: true
tags: ["Sysadmin", "SRE"]
---

## What is SRE?

Reliability : - Statistics people say - "It is the overall consistency of a measure". Engineers say - " concerned with the ability of a system or component to perform its required functions under stated conditions for a specified time". 

Reliability is a function of Mean Time To Failure(MTTF) and Mean Time To Repair(MTTR). 

SRE is what happens when you ask a software engineer to design an operations team. 

A team of engineers who possess the necessary skill set to automate previously manual work even when it is complicated. 

How Google does it? 
The SRE team gets a 50% cap on aggregate ops work. Rest of the time must be used for writing software to automate their hardships.

Tenets of SRE 

* Availability

* Latency

* Performance

* Efficiency

* Change management

* Monitoring

* Emergency response

* Capacity planning


This is where a Performance Engineer rquipped with queueing theory, diagnosis skills, and analysis skills fits great as an SRE. The skill of knowing what to look for and measure correctly is the bread and butter of a Performance Engineer's role - it is every bit as relevant to be an SRE. 

Outage is not a bad thing - it must be an expected part of progress, and should used to learn, evolve and move on. It is important to learn from every outage and think about how it could be _prevented_ in future so we can progress further rather than getting stuck with same category of problems over and over again. 



SRE's goal should be,

Hava a plan : 
When things go wrong, handle the event accurately, quickly, and clean up to restore normal service in a methodic(no panics, no hoping for luck, and no repeating the same thing over and over expecting a different result) way.

Have the skill set to gather enough relevant information to find all root causes of the event.

Have the patience to do a blame-free postmortem with the goal of exposing faults and ideas to mitigate them in future so that they dont need an SRE intervention again. 

### Monitoring:

Keeping track of important things.

Outputs:
1. Alerts - something need immediate human action
2. Tickets - needs asynchronous action by a human
3. Logging - record for diagnostic and forensic purposes

Logs are very useful records to 

* learn how to set up alerting for acute problems

* compare behavior - did that change we put in make the service faster? 

* study resource consumption behavior over time, used for capacity planning 

### Emergency Response

Goal is to quickly and effectively bring the system back to health. This reduces MTTR thereby improving our reliability measure.

Humans add latency. A system that preventively avoids need for human intervention yields better availability. For those other situations that end up needing human intervention, having a playbook for the SRE to quickly work through instead of relying on the engineer's personal strengths would greatly improve MTTR. For those cases that escape the playbook, clear and thorough troubleshooting steps and tips to an engineer can augment the engineer's ability to think on the fly and work through the problem. There must be a feedback loop from whenever an engineer needed to think on the fly - that knowledge needs to be incorporated into playbook or the steps and tips handed to the engineer.

So, 

1 - Build the system such that it does not need human intervention.

2 - If human intervention needed make it workable with playbooks.

3 - If playbooks were not enough, provide troubleshooting steps and tips

4 - If new knowledge was needed to solve the problem, update playbook and troubleshooting guide.

### Change management
70% outages occur due to changes to live system.

3 important notes:

* do progressive rollouts

* quickly and accurately detect problems with rollout

* rollback changes safely when problems arise

Done correctly, the above would minimise or remove human actions from the loop. Humans add latency.

### Capacity planning

Capacity is critical to availability. 

* forecast organic demand.

* incorporate inorganic demand from known historic evidence(feature launches, campaigns, business driving changes)

* regularly perform load testing to correlate raw capacity(compute,disk,network etc) to service capacity(users, transactions etc)

Use Universal Scalability Law to project scalability. 

### Efficiency and performance
This is a very subjective knob for SRE. In most cases, over provisioning adds the cushion to mask performance issues by trading off cost for performance. It takes an SRE to know when and how to manage capacity such that resources are being used efficiently. SREs provision(E.g spin up servers) to meet a capacity target at a specific response speed, and constantly monitors its usage. 


## What does an SRE do?

## Takeaways for small scale


