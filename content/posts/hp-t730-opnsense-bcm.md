---
title: "OPNsense/pfSense on the HP T730: Use Broadcom NICs, not Intel"
date: 2019-11-21T20:45:00-05:00
showDate: true
---

I recently picked up an HP T730 as my OPNsense firewall, mainly to repurpose
my previous HP ProDesk as a desktop. With that, I also initially tried an Intel
NIC primarily because the de-facto NIC choice for OPNsense/pfSense is in fact
Intel. To my surprise, the T730 froze with the Intel-based NICs I tried, both
`igb` and `em` based cards.

Many people have reported on pfSense's subreddit that
[certain Intel-based NICs actually do freeze on the T730](https://www.reddit.com/r/PFSENSE/comments/864mos/help_troubleshooting_kernel_panic/).
However, not all Intel-based NICs freeze.

However, my gut told me that instead of hunting down the right Intel NIC and
hoping it works, just buy a Broadcom-based NIC and ignore the "advice" of the
community. I then decided to then buy a two-port Dell Broadcom BCM5720 for my
T730, and it actully worked. I installed the Broadcom card yesterday and the
system works even today.

I haven't heard the same freezing complaints for Broadcom NICs on the T730, so
it may not have the same issue (but it may). Or the pfSense/OPNsense community
convinced people to only buy Intel so nobody tested Broadcom further.

I'm not saying that Broadcom is perfect. Broadcom Wi-Fi in an older Dell laptop
meant no Wi-Fi on FreeBSD. And maybe Intel is better than Broadcom 99% of the
time. But in the case of the HP T730, just stick with Broadcom for this one.

If you had success (or failure) on the HP T730 with a Broadcom (or any
non-Intel) NIC, please tell me at neel **AT** neelc **DOT** org and I will
(hopefully) update this article.
