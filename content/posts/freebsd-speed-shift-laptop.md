---
title: "Tuning Power Consumption on FreeBSD Laptops and Intel Speed Shift (6th Gen and Later)"
date: 2021-10-21T20:00:00-08:00
showDate: true
---

When running FreeBSD on laptops with a 6th Gen (Skylake) or later Intel CPU,
for me these are HP Spectre x360s (sorry, I'm not a ThinkPad fan), one
annoyance with the out of the box FreeBSD configuration is the fact that the
fan is running most of the time.

In comparison, a HP Envy with an AMD Ryzen does not have this issue with an
out-of-the-box FreeBSD.

One thing that Intel has done with these modern CPUs is include a technology
called **Speed Shift**. While Windows and Linux may have configurations that
automatically optimize for computers with batteries or without, FreeBSD's
default Speed Shift configuration is more optimized for computers without a
battery. Meaning it attempts a "balance" between "performance" and "power
consumption", but this also means the Spectre's fan is always running.

Fortunately, this can be tuned, as per the
[`hwpstate_intel`](https://www.freebsd.org/cgi/man.cgi?query=hwpstate_intel&apropos=0&sektion=4&manpath=FreeBSD+13-current&arch=default&format=html)
man page.

In short, what you need is this in your `/etc/sysctl.conf`:

    dev.hwpstate_intel.0.epp=100
    dev.hwpstate_intel.1.epp=100
    ...
    dev.hwpstate_intel.N.epp=100

Where `N` is the number of threads minus one.

In case you were wondering, the `100` is actually a value between `0` (best
performance) or `100` (most power savings). The default is `50` which attempts
a balance, but I set it to `100` for laptops.

For me, I have a 4-core, 8-thread Intel Core i7-1165G7 in my Spectre x360 14",
and the respective `/etc/sysctl.conf` entry is:

    dev.hwpstate_intel.0.epp=100
    dev.hwpstate_intel.1.epp=100
    dev.hwpstate_intel.2.epp=100
    dev.hwpstate_intel.3.epp=100
    dev.hwpstate_intel.4.epp=100
    dev.hwpstate_intel.5.epp=100
    dev.hwpstate_intel.6.epp=100
    dev.hwpstate_intel.7.epp=100

You can either "reboot" (easier for me), or do `sysctl
dev.hwpstate_intel.0-N.epp=100` manually if you want it done right away.

And enjoy the fan not running all the time! The fan will *still* run, but
less frequently than it did earlier.
