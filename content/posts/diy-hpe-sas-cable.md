---
title: "Building my own HPE SAS cable from Amazon because HPE won't sell me one"
date: 2024-08-07T20:33:00-04:00
showDate: true
---

Remember when [Reddit /r/sysadmin](https://www.reddit.com/r/sysadmin/) said
HPE support blows? Well it does.

I got an open box HPE ProLiant ML110 Gen11 as a NAS.
[This is my second whereas my first is a compute server](/images/homelab_20240728.jpg).
To my surprise, there was no SAS cables in the open box server.

When sourcing the official sources, I was in back and forth conversations with
HPE and their "part suppliers" to no avail. And no, I did not get the right SAS
cable. The worst part, the ticket is still open.

This is something I would expect from Apple, a company making devices with
soldiered everything and a history of fighting repair. I don't expect this from
HPE where service guides are published and unlike Macs, solidered everything is
nonexistent on ProLiants.

So what did I do? Build a SAS cable myself out of parts on Amazon.

The parts are:

 * [SlimSAS LP 8X to 2\*MiniSAS HD 4X,SFF-8654 LP 74pin to 2\*SFF-8643 36pin Cable 80cm](https://www.amazon.com/dp/B0B5D2K1HP) for **$23.88**
 * 2x[CY PCI-Express 4.0 Mini SAS SFF-8087 to SAS HD SFF-8643 PCBA Female Adapter with Bracket](https://www.amazon.com/dp/B0BZ57DMMM) for **$59.98**
 * [6G Internal Mini SAS SFF-8087 to SFF-8087 Cable, 100-Ohms, 0.5-m(1.6ft), 2 Pack](https://www.amazon.com/dp/B017CO6JRO) for **$18.29**

And it's barely above $100. Exactly **$102.15** (excluding sales tax) at the
time of writing.

![The create VM dialog](/images/hpe_ml110_diy_sas.jpg)

Apparently, HPE used a SlimSAS LP (low profile) cable which not widespred is
still not proprietary a la Pentalobe screws or Lightning connectors. The
SlimSAS cable I used is actually a ripoff Dell cable.

While it barely fits with the fan baffle, it works as my Rocky Linux 9 ZFS NAS
and is still $200 cheaper than buying a brand new ML110.

PS: If anyone from HPE is reading this I'd still prefer a genuine 4LFF/8LFF
SAS cable, but if you can't my makeshift cable will work.
