---
title: "A MikroTik RouterOS v7 IPv6 BGP Config"
date: 2024-02-25T12:55:00-08:00
showDate: true
---

As my long-awaited sequel to
[my MikroTik RouterOS v7 BGP configuration](posts/mikrotik-simple-bgp/), I will
do a RouterOS v7 configuration, but this time with IPv6.

The setup will have:

 * **R1** with AS1 and **R2** with AS2
 * 1::/64 that R1 will advertise
 * 2::/64 that R2 will advertise
 * 3::/64 for the point-to-point link between **R1** and **R2**
  - 3::1 for **R1** and 3::2 for **R2**
 * The `ether1` interface for the **R1** and **R2** point-to-point links
 * The `ether2` interface for the internal, to-be-advertised subnet

To setup BGP, first set your IP addresses, on **R1**:

    /ipv6 address
    add address=1::1/64 interface=ether1
    add address=3::1/64 interface=ether2

On **R2**:

    /ipv6 address
    add address=2::1/64 interface=ether1
    add address=3::2/64 interface=ether2

Then configure the IP address lists, on **R1**:

    /ipv6 firewall address-list
    add address=1::/64 list=bgp-networks
    add address=3::/64 list=bgp-networks
    /ipv6 route
    add blackhole dst-address=1::/64

On **R2**:

    /ipv6 address
    add address=2::1 interface=ether1
    add address=3::2 interface=ether2
    /ipv6 route
    add blackhole dst-address=2::/64

Next, we should configure the default AS, on **R1**:

    /routing bgp template
    set default as=1 router-id=1.1.1.1

On **R2**:

    /routing bgp template
    set default as=2 router-id=2.2.2.2

As a note, we do need an IPv4 `router-id` as IPv6 is (sadly) not supported here.

Finally, configure BGP, on **R1**:

    /routing bgp connection
    add listen=yes local.address=3::1 .role=ebgp name=toR2 output.network=\
        bgp-networks remote.address=3::2 templates=default

On **R2**:

    /routing bgp connection
    add listen=yes local.address=3::2 .role=ebgp name=toR2 output.network=\
        bgp-networks remote.address=3::1 templates=default

The BGP should now be set, on **R1**:

    [admin@MikroTik] > /routing/bgp/connection print
    Flags: D - dynamic, X - disabled, I - inactive 
     0   name="toR2" 
         remote.address=3::2 
         local.address=3::1 .role=ebgp 
         listen=yes routing-table=main router-id=1.1.1.1 templates=default as=1 
         output.network=bgp-networks 
    [admin@MikroTik] >

On **R2**:

    [admin@MikroTik] > /routing/bgp/connection print
    Flags: D - dynamic, X - disabled, I - inactive 
     0   name="toR2" 
         remote.address=3::1 
         local.address=3::2 .role=ebgp 
         listen=yes routing-table=main router-id=2.2.2.2 templates=default as=2 
         output.network=bgp-networks 
    [admin@MikroTik] >

# Full configs

If you prefer the raw MikroTik configuration file, here it is.

For **R1**:

    /routing bgp template
    set default as=1 router-id=1.1.1.1
    /ipv6 route
    add blackhole dst-address=1::/64
    /ipv6 address
    add address=1::1 interface=ether1
    add address=3::1 interface=ether2
    /ipv6 firewall address-list
    add address=1::/64 list=bgp-networks
    add address=3::/64 list=bgp-networks
    /routing bgp connection
    add listen=yes .role=ebgp name=toR2 output.network=\
        bgp-networks remote.address=3::2 templates=default

For **R2**:

    /routing bgp template
    set default as=2 router-id=2.2.2.2
    /ipv6 route
    add blackhole dst-address=2::/64
    /ipv6 address
    add address=2::1 interface=ether1
    add address=3::2 interface=ether2
    /ipv6 firewall address-list
    add address=2::/64 list=bgp-networks
    /routing bgp connection
    add listen=yes .role=ebgp name=toR2 output.network=\
        bgp-networks remote.address=3::1 templates=default
