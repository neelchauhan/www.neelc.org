---
title: "FreeBSD 13: Fixing the installer \"Failed to configure bootloader\" error with Manual Partitioning and UEFI"
date: 2020-11-29T20:00:00-08:00
showDate: true
---

On my laptop (HP Spectre x360 2018), I decided to install a second FreeBSD
install, this time on the Intel Optane drive.

When I proceeded to install, I chose manual partitioning, and while it finished
extraction, it proceeded me to this error:

![Failed to configure bootloader error message](/images/fbsd-bl.png)

This happened to me on a recent 13-CURRENT. I don't believe it happened on
earlier snapshots or 12.x. I had this issue with both UFS and ZFS partitioning.

After tesing on my desktop via VirtualBox EFI mode, I found out the issue was
the fact that the EFI system partition wasn't formatted.

**Solution:** To fix this issue, you need to make sure you have a FAT-formatted
EFI system partition. This doesn't mean the EFI partition has to be mounted,
and as of the time of posting the FreeBSD UEFI bootloader won't get loaded.


