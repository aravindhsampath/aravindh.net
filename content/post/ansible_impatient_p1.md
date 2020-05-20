---
title: "Ansible for the impatient sysadmin 👨‍💻/👩‍💻 (part 1)"
date: 2018-08-08T13:27:03+02:00
draft: true
featured_image: "/images/ansible-1.png"
description: "A no fluff guide to start using Ansible for automation. Part 1 - setup SSH & Ansible and tell it where your servers are."
toc: true
tags: ["Ansible", "sysadmin", "Automation", "SSH"]
---
<style>
p {text-indent: 5%;}
</style>
I promised no fluff. This post will give you basic information you need to follow the rest of the post. If you want to know more, open a new tab & Google for whatever information you're looking for. This is _NOT_ an exhaustive guide about everything Ansible can do. Just my notes from learning to use it for what I would like to automate. Consider it a starter which will help you understand what to look up and read on.

### Ansible
Ansible is a combination of a simple automation language([DSL](https://en.wikipedia.org/wiki/Domain-specific_language)) and an automation engine that lets you automate the commands & scripts you run on all of your servers.

Ansible is _agentless_. It executes your automated tasks on the servers over plain SSH. 

_Abstraction_ is a double edged sword. On one end, it helps you simplify a complicated task, on the other end you risk of ending up spending all your time fighting the tools rather than just getting useful work done. So, dont go crazy with using Ansible and try to learn what to do using Ansible documentation. Ansible is just another tool. You're the master who must know _what_ to do. Ansible is just the little devil that does your work on all hosts and document it so that you can refer to what you were doing later.

### Environment
Picture of Nezzar commanding hosts - Nezzar ... Console1 ... Console2 ... OBSD ... 

### Let's set ourselves up - SSH

### Let's set ourselves up - Ansible

### Let's set ourselves up - Inventory

### Our first automation 
get uname from all hosts

See you in part -2
