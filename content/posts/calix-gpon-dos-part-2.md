---
title: "When your Fiber ISP's 'Dumb' Device Screws Your TCP Sessions"
date: 2021-12-16T11:00:00-08:00
showDate: true
---

**UPDATE:** This article is inaccurate. The real issue is another feature
called **Broadcom Packet Flow Cache** which puts a hard limit of 16384 TCP
sessions on the 716GE-I R2. Other ONTs do not have this issue.

When I moved neighborhoods within Seattle, I subscribed to CenturyLink's
fiber-to-the-home, just to learn there was a severe "latency spike" issue when
running Tor. I initially blamed them, while they blamed me in the typcal ISP
fashion.

I can't count how many calls to customer service, tech visits, and even legal
threats via arbitration service [FairShake](https://fairshake.com/) I tried.
I also attempted "quality of service" settings multiple times without success.

Little did I know at the time that the Optical Network Terminal (basically a
fiber modem), the Calix 716GE-I R2, had a "feature" to "prevent" DoS attacks
by limiting the number of concurrent TCP sessions, and denying new TCP sessions
afterwards.

When I learned it was the ONT, I thought there was an overflow in the ONT or
something, but did notice existing TCP sessions were less affected, as was
6rd. This being delibrate is far worse.

An bridge-only ONT like the Calix 716GE-I R2, like a non-routing or bridged
cable or DSL modem shouldn't be limiting the number of TCP connections at once.
If it is deliberately messing with the number of TCP sessions, there must be
something wrong.

In something like AT&amp;T Fiber, it's the forced router limiting the number of
TCP sessions via a tiny NAT table, something that's obvious and in theory could
be "bypassed" via the right exploit (or legislation, if it gets passed). In
comparison, this DoS protection can easily make it seem the ISP is causing the
troubles here, even when they aren't. I thought CenturyLink was a bad ISP when
in fact Calix is the bad player here.

Field techs and network engineers can't solve this "issue". Trust me,
CenturyLink said they got top people involved to no avail.

To demonstrate, let me try web server benchmarking tool `ab` to a public web
server I control:

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

Worse, Calix even admitted to CenturyLink this "feature" is "working as designed":

> We worked closley with Calix and reviewed this finding with them. The Calix 716GE-I ONT device is working as designed by activating Denial of Service (DOS) attack prevention when too many connections are established, which includes jumbo or small packets. Existing connections established before this limit is reached are not affected.

But what if a large number of TCP connections is intentional? I run Tor relays
on my home connection and a Tor relay normally has 10000s of concurrent TCP
sessions. What about BitTorrent or cryptocurrency and Web 3.0 apps? If Web 3.0
takes off, do we exactly want Calix ONTs to limit what we can do with it?

I had to limit the bandwidth of my Tor relay thanks to Calix's dumb "feature",
even when CenturyLink's network is fine and neighbors have no issue.

Or what if you're in a MDU and CenturyLink shares an ONT with 4 units? One user
could easily make the Internet unusable for everyone connected to the same ONT
if one CenturyLink customer sets up their own Tor relay. I tried getting a
second CenturyLink line and it still had issues, because of this "feature".

And worse? [Verizon's next-gen Fiber network will use Calix too](https://www.calix.com/about-calix/verizon-deploys-axos.html),
meaning Verizon FiOS customers may be affected as well once >1 Gbps is
deployed. For comparison's sake, when I had FiOS in NYC, I had no issues with
FiOS on my Tor relay, on both Motorola (now Commscope) and Nokia ONTs, that
with more traffic then what I have now. An Optimum Arris (also now Commscope)
DOCSIS 3.0 modem also had no trouble with forwarding a large number of sessions
on a Tor relay, that a modem from 2013.

To what I know, other ONT vendors such as Nokia, Commscope, Adtran, and Huawei
don't have this "feature". They just foward packets without limiting the number
of sessions.

In many ways, Calix is *worse* than Huawei and ZTE thanks to this "feature".
If I were responsible for building a fiber network and had to choose between
Huawei and Calix, I'd pick Huawei. ZTE versus Calix and I'd pick ZTE. And I'd
stick with this unless Calix removes this "feature" which clearly is mucking
with TCP sessions.

Like how ISPs shouldn't muck with Internet traffic with Net Neutrality (that
Ajit Pai repealed\*). ISP equipment shouldn't do it either. ISPs are dumb pipes,
and ISP equipment shouldn't do things it's not supposed to intentionally.

\* and also blocked me on [Twitter](https://twitter.com/_neelc) along with a
whole cluster of Net Neutrality activists even when I'm just a Microsoft
software engineer working on something non-telecom
