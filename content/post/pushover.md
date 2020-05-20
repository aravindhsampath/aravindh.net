---
title: "Adhoc mobile notifications for a sysadmin using Pushover"
date: 2020-02-09T15:38:54+01:00
draft: false
featured_image: "/images/pushover_header.png"
description: "As a sysadmin, I'd like to get notified of certain events in my infrastructure. Here is how I use Pushover to do it."
toc: true
tags: ["notifications", "Pushover"]
---

## Adhoc notifications

Sometimes, a sysadmin just likes to get notified of certains events occuring in the system - "Backups started" or "/scratch is 80% full" etc.

The simplest way I could find to get this done is using a paid mobile app + API called Pushover.

### How do I do it?

```bash
curl -s --form-string "token=aggrvorgrf85qvk3454kut5ev" --form-string "user=ut5rmuvjr5gdsfhbdrgxzzjqcdfdfhd31" --form-string "message=Gotham scratch disk used above 80 percent" https://api.pushover.net/1/messages.json
```
