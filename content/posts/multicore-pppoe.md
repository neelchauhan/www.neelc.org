---
title: "How to get multi-core PPPoE on your x86 router"
date: 2023-12-22T16:40:00-08:00
showDate: true
---

One commonly-stated problem with PPPoE, especially done on x86-based routers
like pfSense and OPNsense is they're "single-threaded".

The reason why they're single-threaded is because of how NICs are designed.
These NICs know how to sort IPv4 and IPv6 traffic, but not PPP traffic.
combined with both Linux and FreeBSD processing PPPoE in the thread that
process packets.

However, using virtualization and bridge interfaces (**not** PCIe passthrough),
you can mitigate this issue and get real multi-threaded PPPoE.

So what do you need?

# Software

Some routers work very well. Despite the terrible reputation, FreeBSD-based
routers like OPNsense and pfSense can work with multi-threaded PPPoE if your
WAN uses a paravirtualized NIC like `virtio`. VMware and Hyper-V NICs should
also work but I haven't tested those.

However, if you use FreeBSD, you will need the following in your
`/boot/loader.conf.local`:

    net.isr.numthreads=X
    net.isr.maxthreads=X
    net.isr.dispatch=deferred

If you're using a Linux-based router distro, like OpenWrt or VyOS, you will
need to look into
[Receive Packet Steering](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/network-rps),
which does the same thing above on Linux.

Some options to avoid include MikroTik CHR and OpenBSD. Both have
single-threaded PPPoE due to their design. On MikroTik, you can't configure
Receive Packet Steering that I'm aware of. OpenBSD seems to not have kernel
PPPoE or any packet steering option. If you manage to get RPS with CHR, hit
me up at neel **AT** neelc **DOT** org.

# Virtualize &amp; Bridge

Did I mention you need virtualization? Well, you do. And not only that, you
also need to use a bridge interface on the PPPoE WAN.

You may be tempted to use PCIe passthrough, but if you're dealing with PPPoE,
**don't**. If you do, you will still have a single-threaded WAN.

While I'm no Linux kernel expert, using a bridge interface means the host OS
will not process the packet on a single core, but instead just forward it to
the bridge which will then balance it across multiple CPUs.

At least on my host, Rocky Linux 9 with KVM, PPPoE is more-or-less evenly
balanced with OPNsense. However, I have not tested ESXi, Windows, or FreeBSD
virtualization hosts, but I believe it should be the same also.

As a side unrelated note, in the past I was a big FreeBSD user, but moved on
over a year ago outside of OPNsense (but it was more desktop-drive for me).

You **must** use a paravirtualized NIC. On KVM and bhyve, it's `virtio`. on
ESXi it's `vmxnet``. Hyper-V and Xen also have their own respective
paravirtualized NICs. Using this allows the guest to have multi-threaded PPPoE.

# Notes

There are a few things to keep in mind with this setup:

 * Host CPU usage will be higher than the guest
  - Fortunately, it's via multiple threads (via `htop`)
 * You should disable IPv4 and IPv6 on the PPPoE WAN interface
 * If your ISP uses 6rd and you wish to use it, as I do with CenturyLink in Seattle, DO NOT USE Linux-based firewall like OpenWrt, CHR or VyOS.
  - This is because 6rd on Linux-based routers is SLOW when compared to FreeBSD.
  - However, a Linux host OS will be fine.

Also, not related to the technicalities of this article, but around August 2024,
I will be moving to NYC, so I will be unable to test PPPoE after that. This is
because Verizon FiOS doesn't use PPPoE, neither does Spectrum or Optimum if
FiOS is unavailable.

# Test system

I have multi-threaded PPPoE on the following system:

 * HPE ProLiant ML110 Gen11
 * CPU: Intel Xeon Scalable 4410Y (intial), 5412U (current)
 * Host RAM: 32GB (initial), 64GB (current)
 * Guest RAM: 1.5GB RAM
 * Host Cores: 12 (initial), 24 (current)
 * Guest Cores: 3
 * Host OS: Rocky Linux 9.3 (Linux kernel 5.14.0-362.8.1.el9_3)
 * Guest OS: OPNsense 23.07 (FreeBSD 13.2-p1)
 * Virtualization: KVM
 * Host NIC: Broadcom 57416 OCP3 (dual 10GB)
  - two bridges: one WAN and one LAN
 * Guest NIC: `virtio`

You could probably use a much smaller RAM allocation if you just do "typical"
stuff with a few connections. I have 1.5GB RAM is because of my high-bandwidth
Tor relays which really crunch the NAT state table: I have ~65000 states at the
time of writing, and it went up to ~78000.

This is what I get:

![CenturyLink PPPoE Gigabit download](/images/cl-ppp-download.png)

Look ma, balanced CPU usage.
