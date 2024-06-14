---
title: "Taming Noise on HPE ProLiant ML-series Tower Servers"
date: 2024-06-13T14:00:00-04:00
showDate: true
---

[As](https://www.neelc.org/posts/opnsense-pppoe-kvm/)
[mentioned](https://www.neelc.org/posts/hpe-ml110-gen11-ilo-nvme-fan-noise/)
[earlier](https://www.neelc.org/posts/kvm-cockpit/),
my homelab server is a HPE ProLiant ML110 Gen11 which is a single-socket Intel
Sapphire Rapids-based server. One problem with this server is how much noise it
generates. I swear, the ML110 Gen10 was much quieter.

It's a big trouble especially since right now I'm "houseless" meaning I'm
living with my brother and have my ML110 in a bedroom closet. However, with the
default power settings it's still very noisy especially when running a cluster
of Tor relays.

To fix this:

1. Log into iLO.
2. Go to **Power & Thermal**.
3. Click **Power Settings**.
4. In the **Power Regulator Settings**, select **Static Low Power Mode**.
6. you've done that, select **Apply**.

While noise won't go completely, it'll become pretty bearable when inside a
closet. And oh, it'll run cooler.

Keep in mind that there might be some performance penalty as it's on a low
power mode. I haven't done benchmarks so I don't know by how much.
