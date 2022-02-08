---
title: "FreeBSD (or pfSense/OPNsense) on the HP t740 Thin Client"
date: 2022-01-15T16:00:00-08:00
showDate: true
---

While expensive and hard to find on eBay (thanks,
[ServeTheHome](https://www.servethehome.com/)), the HP t740 "Thin Client" is a
great pfSense box if you want more power, or a compact home server.

While I could get away with a t730 or t620 Plus, but I have CenturyLink Fiber
and PPPoE is more computationally intensive versus pure DHCP. That running a
Tor relay, so I'd like some headroom and upgrade path to 10 Gigabits. [1].

# PS/2 Freezes

While the t740 is a neat box, if you plan to run FreeBSD or its derivatives
like pfSense, OPNsense, or HardenedBSD on the bare metal (as opposed to inside
ESXi or Proxmox), an out of the box configuration freezes at boot on:

    atkbd0: [GIANT-LOCKED]

To solve the issue, at the boot prompt, hit `ESC`, and the enter:

    unset hint.uart.0.at
    unset hint.uart.1.at

**Note:** You need to unset both, otherwise it *will* lock up at boot.

After you install, but before rebooting, open a post-installation shell, and
run:

    vi /boot/device.hints

Comment out the `hint.uart.0.at` and `hint.uart.1.at` lines.

This will fix the issue across reboots.

I have run pfSense CE 2.5.2 and am currently running OPNsense 22.1 on my t740
without issues after doing the steps above.

# SSD

If you are using the HP M.2 eMMC, it is not detected on an out-of-the-box
FreeBSD installation. In that case, you will need a third-party M.2 SSD.
I'm using a Samsung 970 EVO 250GB [2], but any M.2 can do, SATA or NVMe.

[1] - Plus after cloning my ONT, I got rid of the connection limit issue.

[2] - I normally buy off-brand "budget" SSDs (don't ask me why), but Best
Buy shipped faster than Amazon when I needed the SSD. The 970 EVO is what
was in stock.
