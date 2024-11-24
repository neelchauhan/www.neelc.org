---
title: "MikroTik CAPsMAN v2 (WifiWave2) with VLANs"
date: 2024-11-24T13:40:00-05:00
showDate: true
---

After a disasterous experiment with Ubiquiti UniFi APs, I decided to sell them
on [/r/homelabsales](https://www.reddit.com/r/homelabsales) (because I'm not
allowed to return) and buy
[MikroTik wAP ax](https://mikrotik.com/product/wap_ax) APs. Interestingly, the
Wi-Fi experience on MikroTik beats the UniFi one despite technically being
"inferior" and the EU model.

But one issue with CAPsMAN is how *hard* it is to configure, especially with a
home network full of VLANs (actually three at home). So how do you configure
it?

First off, if you haven't done so already, you'll need a bridge interface on
the interface connected to the VLAN-trunking switch, due to how MikroTik
designed CAPsMAN:

    /interface bridge
    add name=lan
    /interface bridge port
    add bridge=lan interface=sfp-sfpplus2

Replace `sfp-sfpplus2` with your trunking port.

Secondly, set up the VLANs in the Wi-Fi datapath:

    /interface wifi datapath
    add bridge=lan name=MainSSID-DP vlan-id=2
    add bridge=lan name=GuestSSID-DP vlan-id=3

Replace the information with what corresponds to your network.

Third, set up the Wi-Fi passwords/RADIUS:

    /interface wifi security
    add authentication-types=wpa2-psk,wpa3-psk name=MainSSID-sec passphrase=password
    add authentication-types=wpa2-psk,wpa3-psk name=GuestSSID-sec passphrase=password

Replace the information with what corresponds to your network.

Next, set up the Wi-Fi SSIDs for 2.4GHz and 5GHz:

    /interface wifi configuration
    add datapath=NeelWifi-DP name=MainSSID-2G security=MainSSID-sec ssid=MainSSID
    add datapath=NeelWifi-DP name=MainSSID-5G security=MainSSID-sec ssid=MainSSID
    add datapath=MooWifi-DP name=GuestSSID-2G security=GuestSSID-sec ssid=GuestSSID
    add datapath=MooWifi-DP name=GuestSSID-5G security=GuestSSID-sec ssid=GuestSSID

Replace the information with what corresponds to your network.

Subsequently, set up the CAPsMAN:

    /interface wifi cap
    set discovery-interfaces=sfp-sfpplus2
    /interface wifi capsman
    set ca-certificate=auto enabled=yes interfaces=lan

Replace `sfp-sfpplus2` with your trunking port.

Finally, enable the SSIDs for the `MainSSID` and `GuestSSID` SSIDs:

    /interface wifi provisioning
    add action=create-dynamic-enabled master-configuration=MainSSID-2G slave-configurations=GuestSSID-2G \
    supported-bands=2ghz-g,2ghz-n,2ghz-ax
    add action=create-dynamic-enabled master-configuration=MainSSID-5G slave-configurations=GuestSSID-5G \
    supported-bands=5ghz-a,5ghz-n,5ghz-ac,5ghz-ax

Replace the information with what corresponds to your network.

Abridged configuration:

    /interface bridge
    add name=lan
    /interface bridge port
    add bridge=lan interface=sfp-sfpplus2
    /interface wifi datapath
    add bridge=lan name=MainSSID-DP vlan-id=2
    add bridge=lan name=GuestSSID-DP vlan-id=3
    /interface wifi security
    add authentication-types=wpa2-psk,wpa3-psk name=MainSSID-sec passphrase=password
    add authentication-types=wpa2-psk,wpa3-psk name=GuestSSID-sec passphrase=password
    /interface wifi configuration
    add datapath=NeelWifi-DP name=MainSSID-2G security=MainSSID-sec ssid=MainSSID
    add datapath=NeelWifi-DP name=MainSSID-5G security=MainSSID-sec ssid=MainSSID
    add datapath=MooWifi-DP name=GuestSSID-2G security=GuestSSID-sec ssid=GuestSSID
    add datapath=MooWifi-DP name=GuestSSID-5G security=GuestSSID-sec ssid=GuestSSID
    /interface wifi cap
    set discovery-interfaces=sfp-sfpplus2
    /interface wifi capsman
    set ca-certificate=auto enabled=yes interfaces=lan
    /interface wifi provisioning
    add action=create-dynamic-enabled master-configuration=MainSSID-2G slave-configurations=GuestSSID-2G \
    supported-bands=2ghz-g,2ghz-n,2ghz-ax
    add action=create-dynamic-enabled master-configuration=MainSSID-5G slave-configurations=GuestSSID-5G \
    supported-bands=5ghz-a,5ghz-n,5ghz-ac,5ghz-ax
