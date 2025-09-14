---
title: "Virtualizor: Fixing the \"Required number of IPv4 : 1 and I support : 0\" Error"
date: 2025-09-14T13:55:00-04:00
showDate: true
---

For a living, I run the VPS host [Fourplex](https://www.fourplex.net). I am
launching a storage VPS service alongside our flagship Ryzen 9000 VPS.

When I added the new VPS group, I got this error in WHMCS:

    Error: No server found which fits in the criteria for your VPS configuration
    ...
    Server ID: 8, Reason: XXX.fourplex.net | Required number of IPv4 : 1 and I support : 0

The funny part is in Virtualizor I added the IPv4 and IPv6 pools to the
"Storage" group alongside the "Ryzen" group.

Fortunately, this is a very easy problem to solve. To solve it, log into the
Virtualizor master node and run the following:

    systemctl restart virtualizor

Then you should be able to create VPSes in the new group.
