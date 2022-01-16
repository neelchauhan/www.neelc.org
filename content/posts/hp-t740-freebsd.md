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
Tor relay, so I'd like some headroom [1] and upgrade path to 10 Gigabits [2].

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

I am running pfSense CE 2.5.2 on my t740 without issues [3] after doing the
steps above.

# Footnotes (read: Personal tidbits)

Feel free to ignore if you're uninterested.

[1] - Yes, I previously had "complaints" against CenturyLink, but it turned out
to be the crappy Calix 716GE-I R2 ONT limiting myself to 16384 TCP sessions.
Cloning the ONT to a Calix 803G solves the issue. Calix ONTs are *so easy* to
clone, you just need a JTAG cable.

[2] - I know the t740 can get hot with higher-bandwidth server-grade NICs. I
wouldn't want to push 25 Gbps but when 2/2.5/5 Gbps FTTH comes around maybe I'd
put a 10 GbE NIC.

About the ISP, I *can* get Comcast's 1.2 Gbps (actually 1.4 Gbps) internet, but
since CenturyLink gives me fiber and not DSL, CL is better in every other
metric such as latency, uploads, price, data cap assuming I can clone the ONT
as per [1]. Only compelling feature of Comcast is really real dual-stack IPv6.

When CenturyLink does XGS-PON or something, I'd look into upgrading my NIC as
well, assuming I haven't replaced the box by then.

[3] - I would prefer to run OPNsense, but OPNsense has a nasty bug where my
Android phone constantly disconnects and reconnects every few seconds. This
happens on both Pixel (6) and OnePlus (8, 9 Pro?) devices, on both Netgear Orbi
and Huawei APs when bridged to my OPNsense, and both stock and custom ROMs on
the OnePlus. I'm the type of person who uses root (for VPN hostpot/unlimited
tethering) so iPhones are obviously not an option.
