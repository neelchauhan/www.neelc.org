---
title: "FreeBSD (or Linux) doesn't boot on a HPE ProLiant ML110 Gen10 when using \"Smart Array SW RAID Support\" in UEFI mode"
date: 2019-06-10T18:54:00-04:00
showDate: true
---

**Note:** I am using FreeBSD as the operating system in the article, but the
information should be generic to Linux or any non-Microsoft operating system.
This should also apply to most other HPE ProLiant servers other than the
MicroServer.

I recently got a HPE ProLiant ML110 Gen10 to replace my MicroServer Gen10 as a
home server. So I got a Xeon Silver 4108 ML110 along with two 1TB HPE SATA
drives for a RAID 1 setup. Yes, I'm upgrading within generations, but the HPE
ML110 Gen10 is a pretty neat server and having iLO in a home server for the
first time is a major upgrade.

As a FreeBSD person, the natural thing for me to do when getting a new server
is to install FreeBSD. So I installed FreeBSD in UEFI mode, and added a UEFI
boot entry. When I rebooted, it didn't boot FreeBSD, but tried to boot from the
network.

So I thought did I misspell the UEFI bootloader path? I booted from FreeBSD's
ISO in iLO and re-added the boot entry. The server still tried to boot from the
network.

I did a Google search and found a PDF about HPE's UEFI configuration
([source](https://support.hpe.com/hpsc/doc/public/display?docId=emr_na-a00016376en_us)).
It turns out that the SATA controller on my server was set to "Smart Array SW
RAID Support" by default and that is supported in Windows only. As I am using
SATA and using ZFS mirroring, to be able to boot, I had to set the SATA
controller to AHCI mode to run FreeBSD.

To do this, first reboot your server and press F9 when offered. This will get
you into the UEFI setup. If you are asked for a BIOS password, enter it.

Once you are in the UEFI setup, go to **System Configuration** ->
**BIOS/Platform Configuration (RBSU)** -> **Storage Options** -> **SATA
Controller Options**.

When you are on the **SATA Controller Options** menu, click on the **Smart
Array SW RAID Support** in the **Embedded SATA Configuration** option, click on
OK (it's a warning about legacy BIOS mode that we're not using), and select
**SATA AHCI Support**.

Then you should save the BIOS settings and reboot. To do so, press F12 and
press OK. Now, you should have FreeBSD/Linux/OpenBSD/Illumos booting from disk
in UEFI mode on your ProLiant.
