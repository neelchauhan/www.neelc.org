---
title: "Setting up UniFi Controller on Debian 12"
date: 2025-06-11T13:50:00-04:00
showDate: true
---

While I'm more of a fan of Red Hat-based distros, using a Fedora desktop and
laptop, and multiple Rocky Linux servers, I decided to revisit UniFi after our
NYC townhouse had poor Wi-Fi from MikroTik wAP AXs.

The reality is that UniFi is designed for Debian and Ubuntu as most hobbyists
(and startups) use those. Trust me, I run
[a VPS host](https://www.fourplex.net/) and like 90% of my customers (not
exact) use Debian or Ubuntu.

So I'll install UniFi in a LXC container, but this should work on bare metal
or a VM as well.

**Note:** All commands assume you're logged in as `root`.

To do that, first, you'll need to install the pre-requisite tools for MongoDB
and the UniFi Controller:

    apt install ca-certificates apt-transport-https curl gnupg -y

Then, you'll need to install the MongoDB signing key:

    curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor

Next, add the `apt` repository:

    echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] http://repo.mongodb.org/apt/debian bookworm/mongodb-org/8.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list

Now, install MongoDB:

    apt-get update && apt-get install -y mongodb-org

Now, add the signing keys for the UniFi controller:

    wget -O /etc/apt/trusted.gpg.d/unifi-repo.gpg https://dl.ui.com/unifi/unifi-repo.gpg

And add the `apt` repository:

    echo 'deb https://www.ui.com/downloads/unifi/debian stable ubiquiti' | sudo tee /etc/apt/sources.list.d/100-ubnt-unifi.list

Now, install the UniFi controller:

    apt-get update && sudo apt-get install -y unifi

In a browser, open the UniFi controller web UI by typing in:

    https://X.X.X.X:8443/

Replace `X.X.X.X` with the IP address of the UniFi controller you've just
installed.

From there, it should be self-explanatory.
