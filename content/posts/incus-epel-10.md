---
title: "Installing Incus on AlmaLinux, RHEL or Rocky Linux 10"
date: 2025-09-06T16:00:00-04:00
showDate: true
---

When I set up Rocky Linux on my homelab, I initially set up
[LXD](https://canonical.com/lxd) containers. But when Incus got Rocky Linux 10
images while LXD didn't, I decided to switch.

After all, like most Linux users I never liked `snap` packages.

The problem? The
[Rocky Linux installation instructions](https://linuxcontainers.org/incus/docs/main/installing/)
for Incus points to a Copr repository for Rocky Linux 9, not 10.

While I have two Minisforums running Rocky Linux 9, I also have a HPE ProLiant
ML30 Gen9 running Rocky Linux 10 as a domain controller, UniFi controller and
virtual router for [NYC Mesh](https://www.nycmesh.net/).

I decided then, why not try making my own updated repository?
[So I did](https://copr.fedorainfracloud.org/coprs/neelc/incus/).

# How to install

Because Copr builds EPEL by default on CentOS Stream, I was unable to install
it on Rocky Linux due to Stream having newer packages. So I had to make Copr
build it on RHEL+EPEL.

So `dnf copr enable` won't work. Instead, you have to run the following as root:

    cd /etc/yum.repos.d
    wget https://copr.fedorainfracloud.org/coprs/neelc/incus/repo/rhel+epel-10/neelc-incus-rhel+epel-10.repo

You can then install Incus as normal:

    dnf install incus

However, to use Incus on Rocky Linux, you will also need to set `/etc/subuid`
and `/etc/subgid`:

    echo root:1000000:65536 > /etc/subuid
    echo root:1000000:65536 > /etc/subgid

Now you can run `incus admin init` and create/import containers as normal.

# What about EL 9?

I haven't bothered to build EL 9-capable packages yes. While the Neil
Hanlon-built EL 9 Incus isn't the latest, it works on my two Minisforums
running Rocky Linux 9.

I *might* eventually make EL 9 packages, but don't provide any guarantee.
