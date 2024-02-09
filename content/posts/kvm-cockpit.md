---
title: "An underrated ESXi/Proxmox alternative: Rocky Linux, KVM and Cockpit"
date: 2024-02-08T20:30:00-08:00
showDate: true
---

In my homelab, I have a Rocky Linux 9 server/hypervisor. For quite a while,
I've just used the command line to manage virtual machines. It was tricky to
know which TCP port was used for VNC, and forward it to my Fedora laptop.

I've always been dreaming about a Web UI for virtual machines, but didn't want
ESXi or Proxmox, and was pretty dissapointed when I tried SmartOS and OmniOS.
One day, I was on Mastodon (or Reddit) and learned that there is a KVM virtual
machine module for Cockpit. I was sold.

If you want to know how to set it up,
[Red Hat](https://www.redhat.com/sysadmin/manage-virtual-machines-cockpit) has
a guide. While technically for RHEL, those instructions should also work for
RHEL-like distros, namely Rocky Linux (what I use), AlmaLinux, CentOS Stream,
et al. This article is more of a review than a full-blown tutorial.

Cockpit and the respective `cockpit-machines` isn't a full-on replacement for
ESXi or Proxmox, the feature set is substantly smaller. But for me, who just
wants a minimal hypervisor but also wants bare-metal apps, it's perfect.

# The good

## VNC forwarding

![My Cockpit showing my Windows 2022 Domain Controller's console](/images/vm-pane.png)
My Cockpit showing my Windows 2022 Domain Controller's console

One advantage of Cockpit is that it takes care of VNC consoles. I no longer
have to worry about SSH forwarding and getting the correct VNC port myself.
Yay!

## Creating a VM

![The create VM dialog](/images/create-vm.png)

When setting up a Windows 2022 domain controller in my homelab, I no longer
have to find a very long `virt-install` command. I remember going through my
`.bashrc` just to create a VM every time.

It's easy to create a UEFI VM, and even "automate" the installation with my
own chosen username and password:

![The automated create VM dialog](/images/automate-vm.png)

However, I still prefer to install manually. I don't always trust automated
tools, and this is no exception.

## Migrating VMs

![The migrate VM dialog](/images/move-vm.png)

While at home, I presently have a single hypervisor (HPE ProLiant ML110 Gen11)
and can't obviously test this, there's also a migrate option. This is useful
for companies and homelabs where multiple hypervisors exist.

## Hardware support

The advantage of using Linux over BSD, Windows Server, or ESXi is that a lot
of hardware, even consumer-level PCs, will work well enough.

Windows Server with Hyper-V and ESXi aren't designed for consumer-level PCs,
especially with Realtek NICs or Intel 12th Gen or newer with E-cores. BSD works
on these systems, but only with so-so hardware support. Linux support on these
systems are generally good.

Eventually, I see Windows Server and BSD supporting Intel E-cores too, and
probably ESXi too
([based on Intel's roadmap](https://www.tomshardware.com/news/intel-unveils-new-xeon-roadmap-brings-e-cores-to-the-data-center)).
But then ESXi is no longer free for homelabs, so there's that.

# The bad

## Creating a VM

When creating a virtual machine, it doesn't let you create an image in any
pool, only the "default" one, or you have to select an existing image, which
then can be in any pool.

I have used a custom storage pool and my new domain controller was in the
"default" one. This is even after I disabled the "default" pool; creating a
VM re-enabled it.

## Interfaces

![The create network dialog](/images/create-vm.png)

I use bridge interfaces with my virtual machines. If you want to use a bridge
interface, you'll need a command line to do so. Boo!

For some uses, using NAT is justified, say you're on a dedicated server with
a single public IPv4 address. But in my homelab, I can print ~17 million
RFC1918 IPv4 addresses, and even 5 public IPv4s (via a funky VPN), so it's
less relevant.

# The ugly

## What if I want Debian?

While I haven't daily-driven Debian for over 11 years at the time of writing,
(went from Debian to FreeBSD to openSUSE to now Fedora/Rocky), I've read that
[it's harder to use `cockpit-machines` on Debian](https://www.reddit.com/r/homelab/comments/16gvv2b/comment/k0afqg2/).

`cockpit-machines` is a go-to for fans of Red Hat-based distros like me, but
I won't decide for you if you like Debian, or are wary about Red Hat after they
tried (and failed) to kill "clones". Maybe it's worth it, maybe not.

## What if I want FreeBSD?

I've actually been a FreeBSD user for nearly 10 years, and even a committer.
In fact, Most of my Twitter and Mastodon followers are BSD users. I was a
micro-influencer, but I gave up all away and now barely get any engagement
since I'm now using Linux outside of my OPNsense box.

I know there's [ClonOS](https://clonos.convectix.com/) which wants to be a
FreeBSD hypervisor OS. But if you wanted Cockpit, no, it doesn't exist for
FreeBSD.

But this article isn't about FreeBSD, it's about Rocky Linux and Cockpit.

## Server OS versus Hypervisor OS

Some operating systems, such as ESXi, Proxmox, and SmartOS are designed to be
hypervisors, with the latter two having some level of containerization support.
In turn, they're designed to host multiple systems out of the box.

Other operating systems, such as traditional GNU/Linux distros, BSD variants,
and Windows Server are server operating systems, with a hypervisor added on.
While these operating systems can make perfectly good hypervisors, they may not
be as advanced as a dedicated hypervisor OS.

It's like you could go to your bank if you wanted to invest in stocks, but
you're probably going to have a better experience at a traditional brokerage
like *Fidelity* or *Charles Schwab* (in the US), maybe unless you want to lend
against your portfolio. Same with hypervisors.

For me, I don't need every last feature of ESXi or Proxmox, so
`cockpit-machines` is "good enough" for me, at least after adding a bridge
interface. It also helps since I want my Nextcloud on bare-metal, mainly so it
has most of my free space. But it may be different for you, and probably is.

Disclaimer: At the time of writing, I work at Microsoft. However, I do not work
on Windows, Azure, Hyper-V, or any Linux efforts.
