---
title: "Fixing FreeBSD pkg errors when running \"pkg update\" on Microsoft Azure"
date: 2020-06-01T21:00:00-07:00
showDate: true
---

I work at [Microsoft](https://www.microsoft.com/), and with that, I get free
[Azure credits](https://azure.microsoft.com/).

Considering where I work, I have no use for FreeBSD at work, only Windows.
However, I spent **seven** years of my life prior to getting $DAYJOB using
FreeBSD, going back to high school and basically built my personal setup on
it. I haven't bothered to switch my personal desktop or home server to
Windows (yet\*), so I'll create a FreeBSD VM in Azure and try to update it.

If I create a FreeBSD VM in Azure and update, I get `pkg` errors as follows:

    root@Inst1:/usr/home/neel # pkg upgrade
    Updating FreeBSD repository catalogue...
    Fetching meta.txz: 100%    916 B   0.9kB/s    00:01    
    pkg: repository meta /var/db/pkg/FreeBSD.meta has wrong version 2
    repository FreeBSD has no meta file, using default settings
    Fetching packagesite.txz: 100%    6 MiB   6.5MB/s    00:01    
    pkg: repository meta /var/db/pkg/FreeBSD.meta has wrong version 2
    pkg: Repository FreeBSD load error: meta cannot be loaded No error: 0
    Unable to open created repository FreeBSD
    Unable to update repository FreeBSD
    Error updating repositories!
    root@Inst1:/usr/home/neel #

To fix, this you need to re-bootstrap `pkg` as follows:

    root@Inst1:/usr/home/neel # pkg-static bootstrap -f
    pkg(8) is already installed. Forcing reinstallation through pkg(7).
    The package management tool is not yet installed on your system.
    Do you want to fetch and install it now? [y/N]: y
    Bootstrapping pkg from pkg+http://pkg.FreeBSD.org/FreeBSD:12:amd64/quarterly, please wait...
    Verifying signature with trusted certificate pkg.freebsd.org.2013102301... done
    Installing pkg-1.13.2...
    package pkg is already installed, forced install
    Extracting pkg-1.13.2: 100%
    root@Inst1:/usr/home/neel #

And then update as normal:

    root@Inst1:/usr/home/neel # pkg update -f
    Updating FreeBSD repository catalogue...
    Fetching meta.conf: 100%    163 B   0.2kB/s    00:01    
    Fetching packagesite.txz: 100%    6 MiB   3.3MB/s    00:02    
    Processing entries: 100%
    FreeBSD repository update completed. 31517 packages processed.
    All repositories are up to date.
    root@Inst1:/usr/home/neel # pkg upgrade
    Updaing FreeBSD repository catalogue...
    FreeBSD repository is up to date.
    All repositories are up to date.
    ...
    Proceed with this action? [y/N]: y
    ...

I do recommend updating to a non-EOL release first, as the Azure images may
be out of date, as they are for 12.x.

At the time of writing, I don't work on Azure, so I have no say in how often
FreeBSD images are updated.

\* - Personal desktop/laptop does dual-boot Windows 10 and FreeBSD CURRENT
