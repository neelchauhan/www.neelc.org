---
title: "A Simple IPFW In-Kernel NAT Setup on FreeBSD"
date: 2019-12-25T21:39:00-05:00
showDate: true
---

After graduating [college](https://engineering.nyu.edu/), I am moving from
Brooklyn, NY to Redmond, WA
([guess where I got a job](https://www.microsoft.com/)). I always wanted to
re-do my OPNsense firewall (currently a HP T730) with stock FreeBSD and IPFW's
in-kernel NAT.

Why IPFW? Benchmarks have shown
[IPFW to be faster](https://bsdrp.net/documentation/technical_docs/performance)
which is especially good for my Tor relay, and and because I can! However, one
downside of IPFW is less documentation vs PF, even less without `natd` (which
we're not using), and this took me time to figure this out.

But since my T730 is already packed, I am testing this on a old PC with two
NICs, and my [laptop](https://support.hp.com/us-en/document/c06296486) [1]
as a client with an USB-to-Ethernet adapter.

So you may ask how do we do this?

First, you need the `ipfw` and `ipfw_nat` kernel modules. To load them, run:

    kldload ipfw ipfw_nat

To enable this at boot, put the following in `/etc/rc.conf`:

    kld_list="ipfw ipfw_nat"

Then, you need a firewall ruleset. A basic ruleset is as follows:

    #!/bin/sh

    ipfw -q flush

    ipfw nat 1 config if fxp0 redirect_port tcp 192.168.1.10:9001 9001
    ipfw add 100 nat 1 ip from any to me in via fxp0
    ipfw add 200 nat 1 ip from 192.168.1.0/24 to any out via fxp0
    ipfw add allow ip from any to any

This setup sets up NAT with `fxp0` as the WAN interface, and sets up a port
forward to `192.168.1.10` at port `9001`.

Then, you should enable the firewall in `/etc/rc.conf`:

    firewall_enable="YES"
    firewall_script="/PATH/TO/RULES"

Replace `/PATH/TO/RULES` with the path to the IPFW ruleset, and don't forget to
run `chmod a+x /PATH/TO/RULES` (to be safe).

And enable the NAT firewall with `service ipfw start`.

# What does all this mean?

The first `ipfw` line flushes the existing ruleset, which you should do for
obvious reasons when reloading.

In the second `ipfw` line:

 * The `nat 1 config if fxp0` part sets up NAT with `fxp0` as the WAN interface. Replace `fxp0` with your WAN interface name.

 * The `redirect_port tcp 192.168.1.10:9001 9001` sets up a port forward to `192.168.1.10` with port `9001` [2]. Replace `192.168.1.10` and `9001` with the IP:Port combo you want to forward. This can be repeated multiple times.

The third `ipfw` line allows traffic to come in to the internal network via
`fxp0`.

The fourth `ipfw` line allows outgoing NAT traffic from the `192.168.1.0/24`
subnet to go through `fxp0`. Change `192.168.1.0/24` with the subnet you want
to use. If you have multiple noncontiguous subnets, repeat this line with each
subnet individually.

You can also expand the ruleset, as explained in the
[`ipfw(8)` man page](https://www.freebsd.org/cgi/man.cgi?ipfw(8)).

# Footnotes

[1] - FreeBSD, not Windows at the time of writing. My job could change this,
who knows?

[2] - You will not have NAT reflection/hairpinning. If you need to access an
external domain from your internal network, consider split-horizon DNS.
