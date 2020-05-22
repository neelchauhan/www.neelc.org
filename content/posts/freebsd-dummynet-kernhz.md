---
title: "Use dummynet in a VM? High latency? Set kern.hz to 1000"
date: 2020-05-22T15:00:00-07:00
showDate: true
---

As a hobby, I play with software routers in virtual machines (always FreeBSD).
One recent project of mine was to emulate DSL bandwidth and latency in a VM,
from varying bandwidths of 1.5 Mbps (ADSL) to 50 Mbps (VDSL2).

By default, when using FreeBSD on a hypervisor, the `kern.hz` tunable is set to
`100`. This is to prevent additional CPU use from
[idling](https://www.freebsd.org/doc/handbook/virtualization-guest-virtualpc.html).

But [`dummynet`](https://www.freebsd.org/cgi/man.cgi?query=dummynet&sektion=4&manpath=freebsd-release-ports)
recommends `kern.hz=1000` which is also the default on physical hardware.
This is to prevent additional latency as `dummynet` uses clock ticks, and this
also works with VMs.

Using `dummynet` and the default `kern.hz=100` on both VirtualBox and bhyve
hosts had about 10-20ms delay.

If I set `kern.hz=1000` in `/boot/loader.conf`, the latency from `dummynet`
drops to <1ms on average, and at worst 1-2ms.

However, this has the disadvantage of higher idle CPU use (as mentioned
earlier) in the VM running `dummynet`. I only recommend setting `kern.hz=1000`
in a VM only if you are using something like `dummynet` which relies on clock
ticks and some idle CPU use is acceptable.
