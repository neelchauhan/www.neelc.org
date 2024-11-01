---
title: "openSUSE Tumbleweed and Sony WF-1000XM5/WH-1000XM5 Bluetooth Headphones"
date: 2024-11-01T18:25:00-04:00
showDate: true
---

After my old Sol Republic earbuds died, all the headphones I daily drive are or
have been made by Sony. This includes the WF-1000XM5 earbuds for going out and
the WH-1000XM5 headphones I use on my desk or for plane travel.

While Sony headphones generally work well with Linux (I'm looking at you, Apple
and Beats), I recently switched my Linux desktop and laptop back to openSUSE
Tumbleweed from Fedora.

This came with one interesting problem: Sony Bluetooth headphones won't connect
from GNOME.

The good news is there is a way around it. Open a terminal and:

1. Type in `sudo systemctl start bluetooth` and then `sudo bluetoothctl`

2. Power on Bluetooth via `power on`

3. Scan for devices via `scan on`

4. Put the headphones in pairing mode, wait for them to come up and then grab the MAC

5. Pair the headphones via `pair <MAC>`

6. Quit `bluetoothctl` via `quit`. **This is important** otherwise we'd not be able to connect.

7. Open `sudo bluetoothctl` and then type in `connect <MAC>`

After that, the Sony headphones should be connected via Bluetooth.

Source: [André Sterba's guide for Arch Linux](https://sterba.dev/posts/linux-and-bluetooth/), albeit with modifications.
