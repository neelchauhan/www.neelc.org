---
title: "Optimizing FreeBSD Power Consumption on Modern Intel Laptops"
date: 2022-03-09T20:00:00-08:00
showDate: true
---

My current FreeBSD laptop is a 2020 14" HP Spectre x360, which uses Intel's
11th Gen CPU and "Evo" platform, although I previously also had the 13" 2020
version with a similar setup (but lacked working audio on non-Windows). This
article isn't specific to HP: your shiny-new Lenovo ThinkPad, Dell XPS, or
Framework Laptop can also apply.

One thing with FreeBSD is that unlike Windows or desktop Linux, the default
configuration is poorly optimized for laptops that are newer than your ancient
ThinkPad T420, or maybe a T460s.

Having run FreeBSD on TigerLake on-and-off since December 2020 on two laptops,
there are a few things to keep in mind. These are:

# On TigerLake, go CURRENT for graphics (As of March 2022)

If you want accelerated graphics, as of March 2022, you need to run
`14-CURRENT` branch, or maybe `13-STABLE` (which I haven't tested). 

For graphics, while `drm-kmod` from Ports "works", at the time of writing the
best performance and stability comes with the
[GitHub `master` branch of `drm-kmod`](https://github.com/freebsd/drm-kmod)
which as of writing is at Linux 5.8.

When `14.0-RELEASE` comes out, it should work fine for TigerLake, maybe even
13.1 (although I personally reserve `RELEASE` versions for non-desktops). For
Intel CannonLake (10th Gen) or older, `13.0-RELEASE` or newer should be fine.
For AlderLake (12th Gen) and newer, I don't know if `14.0-RELEASE` will be
supported or not by `drm-kmod`.

# Power consumption

A big problem with newer Intel CPUs is that the fan is constantly running by
default, and these laptops gets *real hot*. It's not just Intel TigerLake, but
also Intel WhiskeyLake (8th Gen Refresh) on an older 2018 13" HP Spectre x360
(which presently is my secondary laptop).

Some of the issues is FreeBSD's default Intel Speed Shift (ISS) configuration
which makes more sense for desktops and servers (or anything lacking a battery)
than laptops.

By default, ISS optimizes the clock for the whole CPU (all cores) than each
core individually. To fix that, you can include this in `/boot/loader.conf`:

    machdep.hwpstate_pkg_ctrl=0

Which will make the ISS control core-based, so if one core is busy your fan
won't be as loud.

You can also optimize ISS for maximum power savings, as opposed to "balanced",
which helps improve battery life even more. If you do this, you need this in
your `/etc/sysctl.conf`:

    dev.hwpstate_intel.0.epp=100
    dev.hwpstate_intel.1.epp=100
    dev.hwpstate_intel.2.epp=100
    ...
    dev.hwpstate_intel.N.epp=100

Where `N` is the number of cores.

The values of this is that:

 * `0` is "maximum performance"
 * `50` is a "balance" (default)
 * `100` is "maximum power savings"

I keep `dev.hwpstate_intel.*` at `100` which I recommend for a laptop, but it's
optional.

# CURRENT notes

If you run `RELEASE` or `STABLE` feel free to skip this section.

However, If you run `CURRENT` as a desktop like me, power consumption can be
improved by running the `GENERIC-NODEBUG` kernel and disabling debugging
symbols in userland. This is a good idea provided you aren't planning to do
kernel hacking on your laptop (or do it in a VM).

To disable debugging symbols for the kernel and userland, include this in
`/etc/src.conf`:

    KERNCONF="GENERIC-NODEBUG"
    WITH_MALLOC_PRODUCTION="YES"
    WITHOUT_LLVM_ASSERTIONS="YES"

Source: [an old Twitter thread](https://twitter.com/FreeBSDHelp/status/1335089284701818881) originated by yours truly.

While not specific to new Intel CPUs, enabling "metadata" mode on `src` builds
can quicken updates. You can find the information on
[FreeBSD's wiki](https://wiki.freebsd.org/MetaMode).

# Results

Even though I can't always stop the fan from running, I can stop it from
running loud most of the time. On a stock FreeBSD, doing about *anything* meant
a loud fan. With FreeBSD tuned according to this document, it's quiet as a
mouse. Well, almost: the fan still runs, and that's normal, but when it does,
it's *much* quieter.
