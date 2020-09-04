---
title: "Setting the IPv6 TTL/Hop Limit on FreeBSD"
date: 2020-09-04T06:00:00-07:00
showDate: true
---

My current wireless service is [T-Mobile](https://www.t-mobile.com/) and I
use a unlocked (Google Store edition) Google Pixel 3 running
[LineageOS](https://lineageos.org/). I am a heavy user of tethering, and unlike
Sprint (switched pre-merger), T-Mobile checks for the TTL to count for hotspot
bandwidth if TTL<=64 (can be bypassed with TTL=65 on a laptop).

My personal laptop happens to run FreeBSD, so I initially thought that setting
`net.inet.ip.ttl` sets both the IPv4 and IPv6 TTL, since at the time I didn't
see a `net.inet6.ip6.ttl`. Then I got a text that I used 80% of my 3GB hotspot.

Looking further, traceroutes showed that while IPv4 had a TTL of 65, IPv6 had a
TTL of 64.

Reading the
[IPv6 man page](https://www.freebsd.org/cgi/man.cgi?query=inet6&sektion=4),
there is a `sysctl` for IPv6 TTL limit, but it is called "hop limit" instead.

The IPv6 TTL/"hop limit" `sysctl` is: `net.inet6.ip6.hlim`.

If you want to bypass the IPv6 hotspot limt (or set the IPv6 TTL), you can add:

    net.inet6.ip6.hlim=65

To `/etc/sysctl.conf`. You can also run:

    sysctl net.inet6.ip6.hlim=65

To change the IPv6 TTL on the running system.

Change `65` to whatever you desire, however `65` is good for bypassing
tethering limits.

Keep in mind that you still need to set `net.inet.ip.ttl` for the IPv4 TTL.
