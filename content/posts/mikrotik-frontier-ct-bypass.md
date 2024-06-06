---
title: "Bypassing Frontier Connecticut GPON 802.1X with MikroTik"
date: 2024-06-06T15:28:00-04:00
showDate: true
---

I've made it back eastwards! Yay! While my family looks for NYC hosing, I'm
living in Stamford, CT in my brother's townhouse/condo.

The condo has Frontier FiberOptic. But as Connecticut is a former AT&T market,
unless you're on XGS-PON which I'm not, GPON is based off AT&T Fiber with the
infamous 802.1X requirement.

Initially, I used a Wi-Fi to Ethernet bridge but after having performance
issues, I moved the Cat6 drops to near my equipment and "bypassed" the Frontier
gateway.

The good news is the older AT&T Fiber bypass methods work on Frontier FiberOptic
in Connecticut. The guide which can be followed is the
[AT&T Bypass thread on the MikroTik forums](https://forum.mikrotik.com/viewtopic.php?t=154954),
namely the bridge method.

There is also a supplicant method but I haven't tested this. And even if
Frontier GPON in Connecticut is based off AT&T Fiber, Frontier is not AT&T so
the certificates may be different. I remember hearing about a decade ago about
AT&T-to-Frontier migration troubles so I'll assume they're different.

You may also be able to bypass on other routers such as pfSense/OPNsense or
Ubiquiti but I haven't tested this. My IPv6 tunneling setup uses L2TP whereas
IPv4 traffic is direct DHCP/IPoE. On MikroTik this works great because I can
set only an IPv6 default gateway to L2TP while ignoring IPv4

Frontier XGS-PON does not need a bypass as Frontier is moving off 802.1X
namely due to the fact that Frontier-acquired Verizon areas never used 802.1X.
If you don't wish to bypass but are fine with a truck roll, ask to get moved
to XGS-PON (if available).
