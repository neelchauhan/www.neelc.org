---
title: "Installing GENI Tools/Omni on FreeBSD"
date: 2019-09-04T10:31:00-04:00
showDate: true
---

I have been asked to use [GENI](https://www.geni.net/) to test some software I
have written at CacheCash/NYU. To communicate with GENI, you usually use a tool
called [Omni](https://github.com/GENI-NSF/geni-tools).

FreeBSD has been my choice of desktop operating system since 2012, and since I
am using my personal machine, my work is being done on FreeBSD.

However, [Omni only supports Python 2](https://github.com/GENI-NSF/geni-tools/wiki/QuickStart#3-install-software-dependencies)
while FreeBSD's default version of Python is Python 3. Not that a default of
Python 3 is a bad thing (I think it's a blessing), but some additional steps
are needed to install run Omni in this case.

First, you need to install the dependencies using the command below:

    sudo pkg install py27-m2crypto py27-dateutil py27-openssl xmlsec1 py27-xmlsec autoconf

Then, you should download Omni's source code which can be found on Omni's
[respective GitHub Wiki page](https://github.com/GENI-NSF/geni-tools/wiki/Getting-GENI-Tools).

Once you have downloaded and extracted the archive, you need to tell Omni and
autoconf to use Python 2. To do that, run:

    setenv PYTHON python2 # To tell autoconf to use Python 2 and not 3
    find geni-tools-2.10 -type f -exec sed -i '' -e 's/env python/env python2/g' {} \; # So the scripts call Python 2

Replace `geni-tools-2.10` with your extracted directory. Keep in mind that you
have to call it from the parent directory of the `geni-tools` directory because
`./` results in **Invalid Command Codes** errors.

Then, you should configure and build Omni. To do that, run:

    cd geni-tools-2.10
    autoreconf -i
    ./configure
    make

Replace `geni-tools-2.10` with your extracted directory.

Now, you should install Omni. To do this, run:

    sudo make install

And after that, you should be able to use Omni and GENI as normal.
