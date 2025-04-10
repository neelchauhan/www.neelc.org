---
title: "ASRock Rack B650D4U/1U2S-B650: Fixing the 0d error on AMD Ryzen 9000-series CPU"
date: 2025-03-03T12:00:00-05:00
showDate: true
---

If you thought HPE support was bad, ASRock Rack support is 100x times worse. But for my startup [Fourplex](https://www.fourplex.net/) branded servers have become cost-prohibitive post-COVID-19. I remember when HPE servers were actually affordable for a homelab.

That being said Fourplex is planning to expand into VPS hosting and I have one 1U2S-B650 (using the B650D4U motherbord) and have three more shipping.

One of the problems with the B650D4U is that the stock BIOS does not recognize Ryzen 9000 CPUs, nor can you flash from USB without booting the system first.

Sadly, you’ll need a Ryzen 7000 CPU to bootstrap the server and initially update the BIOS. A 7600X is good enough for this bootstrap, so if you have one put the heatsink on it, and boot up the system. While a 7600X is normally $299USD but I got it on sale for $229USD at Best Buy.

Next, you’ll have to get the updated [BIOS firmware from ASRock’s website](https://www.asrockrack.com/general/productdetail.asp?Model=B650D4U#Download) in the **Beta Zone and download the 20.02 UEFI.** Unzip the file and place the **B650D4U\_20.02.ROM** file on a FAT32-formatted USB drive.

Next, when the server is booting up with the 7000-series CPU, press F6 for Instant Flash during the POST. Subsequently navigate to the ROM file and update the UEFI.

After the UEFI has been updated, unplug the server and install the newer Ryzen 9000 CPU. I am using a 9900X as the “stock” CPU and it works after the UEFI update and the 7600X “boostrap”.

Now if only the ASRock Rack IPMI wasn’t so unreliable. It was unreliable on a X470D4U and a Ryzen 3700X and still is unreliable even now.
