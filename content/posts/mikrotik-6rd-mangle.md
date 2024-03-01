---
title: "Enabling Path MTU Discovery in MikroTik, or why my PPPoE/6rd was slow"
date: 2024-02-29T21:00:00-08:00
showDate: true
---

For many years, I've stuck with OPNsense, first initially since until a couple
of years ago I was a die-hard FreeBSD user. But more importantly, by default
Linux-based firealls play poorly with CenturyLink's 6rd.

I've been wanting to use a MikroTik as my core router instead of OPNsense for
many years, but whenever I tried, 6rd browsing was just so slow for some
reason.

A few days ago, I got myself a MikroTik CCR2004-16G-2S+ and intially went
IPv4-only. But felt very guilty about it, and wanted my IPv6 back. I did some
research, and found out that unlike OPNsense,
[MikroTik didn't enable Path MTU Discovery by default](https://forum.mikrotik.com/viewtopic.php?t=162930#p802790).

To fix it, add the following config (source: forum article above):

    /ipv6 firewall mangle
    add action=change-mss chain=forward new-mss=clamp-to-pmtu passthrough=yes \
        protocol=tcp tcp-flags=syn

Path MTU Discovery for some reason isn't enabled on Linux-based distributions,
not just MikroTik but also OpenWrt and Netgear Orbi's stock firmware.

While I won't be needing this configuration for very long, I'm moving back to
NYC and will likely have Verizon Fios without the PPPoE and 6rd nonsense. But
in the meanwhile, do we really want more IPv4-only traffic?

But had I known, I could've gone with MikroTik all along instead of settling
for inefficient x86 boxes. Instead, I once even dealt with a psycho eBay
seller for firewall hardware that could've been avoided if I read the linked
post a year back.
