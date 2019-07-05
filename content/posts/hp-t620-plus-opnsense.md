---
title: "Fixing the \"Segmentation Fault\" when installing OPNsense on the HP T620 Plus"
date: 2019-07-05T15:36:00-04:00
showDate: true
---

I have gotten a HP T620 Plus as a firewall box, replacing a Chinese "Mini PC"
which barely handles my 300/300 Verizon FiOS. The T620 Plus is a very popular
choice in the pfSense world, however I wanted to go with OPNsense.

One issue with the T620 Plus with OPNsense is that when you attempt to install,
you get a segmentation fault in the installer. From forum posts, many other
people have had this issue with HP and non-HP hardware.

The issue is that the OPNsense installer uses a client/server setup that doesn't
play well with the full-screen UEFI console, and OPNsense doesn't boot on the
T620 Plus in BIOS mode because HP's UEFI doesn't want to boot BIOS from GPT
disks.

To fix this issue of segfaults from the installer, you need to boot in UEFI
mode from the USB containing OPNsense. When you are at the OPNsense loader
screen (with the countdown), press `Esc`.

When you are at the loader prompt starting with `OK`, type the following:

    set gop 0

And press enter. The screen should go blank. When it does, type in the
following to boot (even if there is no output):

    boot

And press rnter. After the above step, you should get a (lower-resolution)
console that doesn't bug out with OPNsense's installer. Then you should be able
to install OPNsense normally.

You do not need to put `set gop 0` in `/boot/loader.conf` on the installed copy
of OPNsense.
