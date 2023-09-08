---
title: "Forwarding Ports 80 and 443 on OPNsense Correctly"
date: 2023-06-20T09:30:00-07:00
showDate: true
---

If you're like me and run your own home server, you might find yourself
needing to forward TCP ports 80 and 443 on your router. I recently
changed my firewall from OpenWrt to OPNsense and obviously needed to
forward ports 80 and 443 to my home server, a M1 Mac Mini running Fedora
Asahi Remix.

By default, OPNsense tries to listen it's web UI on all ports, well sort of.
Many suggestions online say you should change the port the web UI listens on.
I'll tell you that suggestion doesn't work for me at all.

If you need to port forward TCP ports 80 and 443 correctly, here's what you
need to do:

1. Log into your OPNsense web portal. Most likely it's `192.168.1.1` unless you changed it like me.

2. Go to **Firewall** -> **Settings** -> **Advanced** on the sidebar

3. In the **Network Address Translation** section, check **Reflection for port forwards** and **Automatic outbound NAT for Reflection** and then click **Save**

4. Go to **NAT** -> **Port Forward** and add or edit your existing port forwards for 80 and 443

5. In the **NAT reflection** section, select **Enable**

6. When it asks you to save settings, select **Apply changes**

And that my friend is how to correctly forward Port 80 and 443 in OPNsense.
