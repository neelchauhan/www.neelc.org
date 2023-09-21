---
title: "Taming Fan Noise on HPE Gen11 Servers and Third-Party NVMe Drives"
date: 2023-09-21T15:15:00-07:00
showDate: true
---

I recently got a HPE ProLiant ML110 Gen11. While it cost far more than the
previous generation thanks to COVID-19, the trade war, and supply chain
issues, it is still an excellent server.

One thing with HPE servers is that by default, if you use a third-party NVMe
drive, or any PCI Express card that isn't blessed by HPE, the fan becomes very
loud. By very loud, I mean I can hear it from a shut closet. I even had some
family come over and complain how loud it is.

To add to the fire, I'm planning to move back to NYC from Seattle (no timeframe
however) and while NYC is noisy, I don't want to hear buzzing noise when I sleep
especially in a small apartment.

To make the server quiet with a third-party NVMe drive, you can use this cURL
command on the iLO "Redfish" API:

    curl --request PATCH --url 'https://[ILO_IP]/redfish/v1/Chassis/1/Thermal/' --user '[ILO_USER]' --header 'content-type: application/json' --insecure --data '{"Oem": {"Hpe": {"FanPercentAdjust": 50}}}'

Replace `ILO_IP` with the iLO IP address and `ILO_USER` with the iLO Administrator
(usually `Administrator` but for me I add a `neel` account).

You can also replace the `50` with a higher or lower value to make it louder
or quieter respectively, but don't go so quiet to make your server overheat.

After that, the server is pretty quiet, whether the closet is open or shut.

Credit: [@Itzikbenabu from the HPE forums](https://community.hpe.com/t5/proliant-servers-ml-dl-sl/fan-noise-ml-350-gen-11/m-p/7197000/highlight/true#M183355).

# What about HPE `amsd`?

While a common solution is HPE's `amsd`, that didn't work for me with NVMe
drives. It is more for SATA and SAS drives.

iLO also couldn't see the Rocky Linux version of `amsd`, but the RHEL version
was seen by iLO fine even on Rocky. Even when iLO sees `amsd`, the server was
still noisy before I ran the above Redfish command.
