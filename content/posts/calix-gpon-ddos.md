---
title: "How To DoS and Take Down Your GPON Fiber Modem/ONT with Small Packets"
date: 2021-12-11T22:00:00-08:00
showDate: true
---

**UPDATE 2:** This article is inaccurate. The real issue is another feature
called **Broadcom Packet Flow Cache** which puts a hard limit of 16384 TCP
sessions on the 716GE-I R2. Other ONTs do not have this issue.

**UPDATE:** CenturyLink contacted Calix and it seems the limiting of TCP
sessions is intentional. [This isn't right](/posts/calix-gpon-dos-part-2/).

When I moved neighborhoods in Seattle, I learned my new address had CenturyLink
Fiber. I was initially excited, but the excitement wore off when I learned when
I run a Tor relay on CL, it gave latency spikes. This can become severe based
on how much bandwidth Tor is allowed to use.

As a starter, if you don't know what terms like GPON and ONT are, I'll explain
below:

 * **GPON** (Gigabit Passive Optical Network) is a technology to split one fiber from a telecom provider's headend between multiple users

 * An **ONT** (Optical Network Terminal) is a device to convert a GPON (or other fiber) signal to an Ethernet, Coax, or Telephone handoff, not unlike a DSL or Cable modem

I initially blamed CenturyLink's network, and they blamed me in the typical
ISP fashion. Actually learning it wasn't their network, my OPNsense, or the
GPON node I'm on was a shock (neighbors don't spike).

While QoS was initially suggested, but after some investigation and tests with
`iperf3`, I figured out it was easy to do a denial-of-service to take down a
Calix's 716GE-I R2 GPON ONT with an absurd number of small packets, at least
for the time the packets are being transmitted.

In short, it's not me and not CenturyLink, but Calix for not taking small
packets into account very well.

How is it not a "defect" in the particular ONT I have? I had it replaced and
the "replacement" still spikes.

Going forward, let me try Apache's `ab` to a public web server I control:

    root@fatbox:/home/neel # ab -n 5000 -c 1000 http://X.X.X.X/
    This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
    Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
    Licensed to The Apache Software Foundation, http://www.apache.org/

    Benchmarking X.X.X.X (be patient)
    Completed 500 requests
    ..
    Completed 3500 requests
    apr_socket_recv: Connection reset by peer (54)
    Total of 3853 requests completed
    root@fatbox:/home/neel #

Don't worry, it's a dedicated server I run a Tor exit relay on, so I didn't
DDoS someone else by accident or make fellow VPS customers unhappy.

During the `ab` session, I saw the latency and packet loss went out of control:

    neel@fatbox:~ % ping 1.1
    PING 1.1 (1.0.0.1): 56 data bytes
    64 bytes from 1.0.0.1: icmp_seq=2 ttl=58 time=434.083 ms
    64 bytes from 1.0.0.1: icmp_seq=5 ttl=58 time=2385.823 ms
    64 bytes from 1.0.0.1: icmp_seq=9 ttl=58 time=1141.932 ms
    64 bytes from 1.0.0.1: icmp_seq=10 ttl=58 time=163.309 ms
    64 bytes from 1.0.0.1: icmp_seq=11 ttl=58 time=1974.494 ms
    64 bytes from 1.0.0.1: icmp_seq=13 ttl=58 time=1831.108 ms
    64 bytes from 1.0.0.1: icmp_seq=15 ttl=58 time=1359.357 ms
    64 bytes from 1.0.0.1: icmp_seq=19 ttl=58 time=312.364 ms
    64 bytes from 1.0.0.1: icmp_seq=20 ttl=58 time=10.901 ms
    64 bytes from 1.0.0.1: icmp_seq=21 ttl=58 time=17.493 ms
    64 bytes from 1.0.0.1: icmp_seq=22 ttl=58 time=9.149 ms
    64 bytes from 1.0.0.1: icmp_seq=23 ttl=58 time=5.793 ms
    ...
    64 bytes from 1.0.0.1: icmp_seq=34 ttl=58 time=5.178 ms
    ^C
    --- 1.1 ping statistics ---
    36 packets transmitted, 23 packets received, 36.1% packet loss
    round-trip min/avg/max/stddev = 5.178/602.379/2385.823/772.647 ms
    neel@fatbox:~ %

And my bandwidth usage still stayed low during this:

                    /0   /1   /2   /3   /4   /5   /6   /7   /8   /9   /10
     Load Average   |

      Interface           Traffic               Peak                Total
            lo0  in      0.000 KB/s          0.000 KB/s            8.984 MB
                 out     0.000 KB/s          0.000 KB/s            8.984 MB

            re0  in      0.768 KB/s        293.526 KB/s            2.623 GB
                 out     0.256 KB/s        159.354 KB/s          509.043 MB

The nature of the attack is that you can easily DoS your own Calix ONT if you
push hard, but not others in the neighborhood (as I was told). But it could be
abused if an attacker wants to kick someone offline if (a) they have open ports
and (b) you know their IP (e.g. DDNS).

The worse part is, a larger amount of Tor traffic on Verizon FiOS in NYC using
both a Motorola and Nokia (or maybe Arris) ONT did not get these spikes, so
seems to be a Calix-specific bug, whereas a smaller amount of Tor traffic can
crush my CenturyLink Fiber connection easily.

I don't know the situation around other GPON vendors such as Adtran and Huawei
as I never had GPON ISPs other than Verizon and CenturyLink. I had other
Symmetrical Gigabit ISPs which used Active Ethernet and wireless radios, as
well as (non-Gigabit) cable ISPs at my dad's place, neither which had these
same spikes.
