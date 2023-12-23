---
title: "Install Folding@home on Fedora 39 with FAHControl"
date: 2023-12-22T22:45:00-08:00
showDate: true
---

At the present moment, my desktop is technically a "gaming PC" but really a
workstation for various non-gaming tasks. It's a Mini-ITX homebuilt PC with an
Intel i9-13900F CPU and a RTX 4070. It runs Fedora 39.

I've been wanting to run Folding@home on my main PC for a while now. I run
Folding@home at my work systems, both Windows 365 and physical workstation
(although I mostly WFH, yay!).

One problem with using the default binaries from the Folding@home website is
FAHControl is designed for Python 2. Considering Fedora is the cutting edge
distro we know it is, it's no surprise they'll deprecate software like candy
wrappers.

If you're just using it "headless" without a GUI, the binaries on the
Folding@home website **are** what you want.

But if you want a GUI, fortunately, there is a Snap package. Yes, I know it's
Snap and not Flatpak, and that Snap is the HD-DVD or CDMA of Linux packaging,
but that's where it's on.

To install Folding@home, you need to run the following:

    $ sudo dnf install -y snapd
    $ sudo snap install folding-at-home-fcole90

The `systemd` service will also automatically be configured.

If you want FAHControl, you will need to open a terminal and run:

    $ sudo folding-at-home-fcole90.FAHControl

Yes, you will have to run that command when you want FAHControl, as it isn't
in my GNOME menu.

And there you have it, Folding@home with FAHControl on Fedora 39!

![Folding@home with FAHControl on Fedora 39](/images/fedora-folding.png)
