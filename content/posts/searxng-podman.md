---
title: "Installing SearXNG on AlmaLinux/RHEL/Rocky Linux with Podman and SELinux"
date: 2025-10-12T18:10:00-04:00
showDate: true
---

While I've never really used DuckDuckGo, due to me liking Google results better
(although I hate the data collection), I was bored today and wanted something
in my home lab.

When checking [awesome-selfhoted](https://github.com/awesome-selfhosted/awesome-selfhosted)
I thought "why not try SearXNG" after seeing someone else run it too.

On my home lab, I run two Minisforum MS-01s as compute nodes, both running Rocky
Linux 9 with a mix of Incus, Podman and Cockpit KVM.

One of the issues with SearXNG in Podman is SELinux permissions. While an awful
lot of sysadmins disable SELinux, I prefer to keep it enabled unless it's
required to disable it (I'm looking at you, Virtualizor) or I'm in Incus where
containers don't have SELinux.

To install SearXNG in Podman, first create the directories:

    mkdir /var/container/searxng/{config,data}/

Next, set the SELinux permissions:

    semanage fcontext -a -t container_file_t "/var/container/searxng/config/”
    semanage fcontext -a -t container_file_t "/var/container/searxng/config/(.*)?”
    restorecon -Rv /var/container/searxng/config/


Then, run the Podman container:

    podman run --name searxng -d -p 10002:8080 -v "/var/container/searxng/config/:/etc/searxng/" -v "/var/container/searxng/data/:/var/cache/searxng/" docker.io/searxng/searxng:latest

If you want SearXNG to persist after reboots, add the following to
`/etc/containers/systemd/searxng.container`:

    [Container]
    ContainerName=searxng
    Image=docker.io/searxng/searxng:latest
    PublishPort=8888:10002
    Volume=/var/container/searxng/config/:/etc/searxng/
    Volume=/var/container/searxng/data/:/var/cache/searxng/

I also set up a reverse proxy for my LAN via Caddy, but this is out of scope
here.
