---
title: "Have an ASN and IPv6 space? Build your own IPv6 tunnel!"
date: 2024-09-03T20:56:00-04:00
showDate: true
---

For many years, Hurricane Electric was the *de-jure* IPv6 tunneling platform.
If you wanted Netflix, just force Netflix on IPv4. For people without native
IPv6, HE.net was truly a godsend.

Then HE.net tunnels became more problematic, now we have multiple streaming
services and other services blocking HE.net tunnels under the "public proxy"
blanket ban. I remember the pre-COVID and the early-COVID era when only Netflix
blocked HE.net tunnels when I lacked native IPv6 until summer 2020. And now,
I'm not going to get the hostnames of every streaming service my mom uses to
block IPv6 on those.

This led me to think, if someone has an ASN, IPv6 space, and a BGP-capable VPS,
why not make it an IPv6 tunnel? I have my own ASN
([AS33535](https://bgp.he.net/AS33535)), IPv6 space 2602:2e6::/36, a
[BuyVM BGP-capable VPS](https://buyvm.net/) and even a /23 equivalent of IPv4,
although I'll give up all those up any day for 100% IPv6 deployment and a IPv4
shutdown.

Going back, since I moved back to the NYC-area, BuyVM made the most sense due
to a low price, unlimited bandwidth, great support and most importantly, a NYC
PoP. [Vultr](https://www.vultr.com/) is another (more expensive) option with a
truly global network.
[There are many other BGP VPS hosts as well](https://bgp.services/bgp-vps-providers).

To get an ASN if you're in an ARIN region (USA or Canada) you basically need
to be a corporate entity to get IP space, but at least for Americans
registering a company is so easy my homelab has a LLC, complete with a business
bank account and credit card. You don't even need a LLC, a sole proprietorship
is enough for this. You can however lease IPv6 from a company such as
[Free Range Cloud](https://freerangecloud.com/) if you don't want an "ISP" or
"End User" allocation but getting an ASN is a $550 one-time fee.

In Europe or the Middle East, you can use a Local Internet Registry (LIR) to
get IPv6 space and an ASN. These are usually affordable subscription services
also offered to individuals. RIPE is much better than ARIN for this which makes
it easier for people who don't want the hassle of registering a business. Sure,
I might be willing to but most people aren't and this is where RIPE truly
shines.

I'm not sure about other RIRs. I believe APNIC is impossible for leasing IP
space but could be wrong. I'm not sure about AfriNIC or LANIC. **Do your
research**.

Also, you don't need IPv4 space as the VPS IPv4 is enough for the tunnel.

For the software speaking BGP, you can use a router OS such as
[MikroTik CHR](https://help.mikrotik.com/docs/display/ROS/Cloud+Hosted+Router%2C+CHR)
like me, [VyOS](https://vyos.org/) if you want FOSS, or can roll one from a
standard Linux/BSD distro. I do not recommend "firewall" distros such as
pfSense or OPNsense for this as they're not designed to be a "router" or a
"BNG" (broadband network gateway) as much as they're a "firewall". Remember,
our IPv6 "tunnel" is effectively the same thing many ISPs do on a "unbundled"
DSL or fiber network. "Firewall" distros are fine for your clients.

Then you need the protocol. Two protocols are 6in4 or L2TP. 6in4 is what
Hurricane Electric and other tunnel brokers use, but doesn't work behind NAT
or CGN and requires reconfiguration (whether manually or via scripts) whenever
your IP changes. I don't recommend this for a makeshift IPv6 tunnel if you lack
a static IPv4 address, well unless somebody comes up with a update IP script.

I'm a fan of L2TP. It's easier on the service side, but is a bit more complex
on the client side and has more overhead. Meaning you'll have to make sure L2TP
isn't used as an IPv4 default route if you want your ISP for IPv4 routing. So
far I have MikroTik and FreeBSD clients to my IPv6 L2TP tunnel, both acting as
routers to their respective networks.

If you're interested in my CHR config, a stripped down config is below:

    /interface ethernet
    set [ find default-name=ether1 ] disable-running-check=no
    /ip pool
    add name=ppp-pool ranges=100.75.64.1-100.75.64.255
    /ppp profile
    set *0 local-address=100.75.64.0 remote-address=ppp-pool use-encryption=no
    /routing bgp template
    set default as=33535 router-id=198.98.XX.XX
    /interface l2tp-server server
    set allow-fast-path=yes enabled=yes
    /ip dhcp-client
    add interface=ether1
    /ipv6 route
    add blackhole dst-address=2602:2e6:X::/48
    add gateway=2605:6400:XX::1
    /ipv6 address
    add address=2605:6400:XX:X::1/48 advertise=no interface=ether1
    /ipv6 firewall address-list
    add address=2602:2e6:X::/48 list=bgpnet
    /ppp secret
    add name=yser1 remote-ipv6-prefix=2602:2e6:X::/56 service=l2tp
    add name=user2 remote-ipv6-prefix=2602:2e6:X:XXX::/56 service=l2tp
    /routing bgp connection
    add address-families=ipv6 disabled=no listen=yes local.address=\
        2605:6400:X:X::1 .role=ebgp multihop=yes name=buyvm output.network=\
        bgpnet remote.address=2605:6400:ffff::2 .as=53667 templates=default

For the IPv4, since I'm not using IPv4 I put in dummy CGN IPv4 addresses
for the L2TP but in reality I'm announcing IPv4 too.
