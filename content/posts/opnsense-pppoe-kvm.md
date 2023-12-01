---
title: "Want multi-threaded PPPoE in OPNsense/pfSense: Virtualize it with bridges"
date: 2023-11-13T21:30:00-08:00
showDate: true
---

I am currently a CenturyLink Fiber customer in Seattle, WA and its well known
that CenturyLink uses PPPoE. Yes, I'm aware of the migration to "Quantum Fiber"
which uses DHCP, but I'll probably move to NYC before I get shifted to Quantum
and subsequently have Verizon FiOS (again), also with DHCP.

My home server, a massive
[HPE ProLiant ML110 Gen11](https://buy.hpe.com/us/en/compute/tower-servers/proliant-ml100-servers/proliant-ml110-server/hpe-proliant-ml110-gen11/p/1014705725)
with a Broadcom 10GbE OCP adapter, I virtualize OPNsense inside of Rocky Linux
9 using KVM, which is directly connected to my
[(hacked) CenturyLink ONT](https://neelc.org/posts/clone-calix-ont/).

A well-repeated myth is that FreeBSD (the base OS of OPNsense and pfSense)
is that [PPPoE is single-threaded](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=203856). As a former FreeBSD committer, I have some understanding on how
this works.

The reality is that physical multi-queue NICs only sort native IPv4 and IPv6
across all queues, and use queue 0 for incoming (download) PPPoE traffic.
Operating systems (Linux included) process incoming PPPoE frames using the
first CPU core on these NICs. However, upstream traffic is generally evenly
balanced regardless of OS as the queues are chosen in the operating system.

One way around this is to virtualize OPNsense or pfSense **if** you use a
bridge interface for the WAN and the `virtio` NIC. This way, assuming I got it
correctly, Linux simply forwards the Ethernet frames on the bridge and doesn't
do any processing. The VM and `virtio` NIC processes PPPoE frames with all
cores in the VM.

Don't believe me, look at this:

![CenturyLink PPPoE Gigabit download](/images/cl-ppp-download.png)

Look Ma, evenly balanced multithreaded PPPoE download.

However, if you use PCIe passthrough, you will still have the same issue with
single-threaded PPPoE, since it's still the same NIC driver.

If you are using FreeBSD, OPNsense, or pfSense, you will still need these
`/boot/loader.conf.local` tweaks as follows:

    net.isr.numthreads=X
    net.isr.maxthreads=X
    net.isr.dispatch=deferred

Replace `X` with the number of CPU cores allocated to the VM.

If you are running Proxmox, or another Linux distro (e.g. Debian), it should
also work since it's the same Linux kernel. I haven't tested other operating
systems or hypervisors, but it hopefully should be multithreaded there too.

There is one disasvantage if you are virtualizing FreeBSD on KVM: the KVM host
*will* use a lot of CPU usage, more than what FreeBSD will show. ~~FreeBSD is
poorly optimized for KVM, which kinda [reminds me of MS-DOS and Windows 9x/ME
with no idle states whatsoever](https://superuser.com/questions/369388/cpu-running-at-full-capacity-when-boot-to-dos),
just a lot more subtle.~~ I tried MikroTik CHR, and it also has high PPPoE CPU
usage, but unlike OPNsense has no option (that I know of) to balance PPPoE
traffic. The host CPU could be PPPoE processing, IP routing, `virtio`, or
something else I don't know about.

![KVM CPU Usage off the roof](/images/cl-ppp-kvm-cpu.png)

Crap, high CPU usage thanks to speedtests.

But regardless, it's still better than putting everything on CPU 0. Just that
if you have multi-Gigabit XGS-PON/10G-EPON PPPoE (e.g. Bell Canada, UTOPIA, NTT
OCN), you'll have to stock up on CPU cores.
