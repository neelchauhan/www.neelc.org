---
title: "Bypassing AT&T Fiber/Frontier/AU 802.1X with MikroTik and bridge interfaces"
date: 2024-09-17T14:20:00-04:00
showDate: true
---

Although I now live primarily in Verizon territory, my family has a second home
in Frontier-land at least for a few more months. Frontier in Connecticut
inherited AT&amp;T's 802.1X setup so if you're not on XGS-PON, you are required
to use Frontier's router, in my case an Arris NVG468MQ.

However, if you're using a MikroTik CCR2004-series router, you can use that
connected to the ONT and bridge 802.1X from the Arris. You sadly cannot do this
with a RouterBOARD (trust me, I've tried), but you may also be able to do this
on a CCR2216.

You will need two free Ethernet ports, one for your WAN (obviously) and one for
bridging 802.1X from your AT&amp;T/Frontier/AU router.

**Note:** While I don't have AT&amp;T Fiber, if you are using AT&amp;T Fiber
there are newer bypass methods which aren't dependent on bridging 802.1X. The
[8311 Discord Server](https://discord.gg/8311) has information on this.

So you wanna bypass? First, log into the serial console and paste this:

    /interface bridge
    add name=WAN admin-mac=00:00:00:00:00:00 pvid=111 auto-mac=no igmp-snooping=yes protocol-mode=none vlan-filtering=yes
    /interface bridge port
    add bridge=WAN interface=ether1
    add bridge=WAN interface=ether2
    /ip dhcp-client add dhcp-options=clientid disabled=no interface=WAN use-peer-dns=no use-peer-ntp=no
    /system scheduler add name=OnRebootATT start-time=startup on-event=":delay 30\r\n/system script run OnRebootATT"
    /system script add name=OnRebootATT source="#\_OnRebootATT\r\n\r\n:log info \"Script: Starting OnRebootStartATTRG\";\r\n:delay 5\r\n\r\n:log info \"Script: Enable Virtual switch for ONT and ATT RG\";\r\n/interface bridge set WAN pvid=111\r\n\r\n:log info \"Script: Ensure ATT RG ether2 is visible to ONT\";\r\n/interface bridge port set bridge=WAN [find interface=ether2] pvid=1\r\n/interface ethernet enable ether2\r\n\r\n:log info \"Script: Sleep for 3 minutes to allow ONT and ATT RG time to sync\";\r\n:delay 180\r\n\r\n:log info \"Script: Ensure ATT RG is NOT visible to ONT\";\r\n/interface bridge port set bridge=WAN [find interface=ether2] pvid=222\r\n/interface ethernet disable ether2\r\n\r\n:log info \"Script: ONT and ATT RG should be in sync. Virtual Switch shutting down. Enjoy your router.\";\r\n/interface bridge set WAN pvid=1\r\n"

Keep in mind that:

 * Replace `00:00:00:00:00:00` with the 802.1X-speaking router's WAN MAC address
 * Replace `ether1` with the interface connected to the ONT
 * Replace `ether2` with the interface connected to the 802.1X-speaking router
 * It will take a while to boot up, since 802.1X has to authenticate via a "bridge" then get shut down
 * If you unplug the ONT cable, you will have to reboot your router

After doing that, reboot your router, plug in the 802.1X-speaking router to
`ether2` and the ONT to `ether1`, and reboot again. After 4-5 minutes you'll
be online, bypassed! Yay!
