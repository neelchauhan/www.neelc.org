---
title: "Automatic Switchover between Headphones and Speakers on FreeBSD Laptops with device.hints"
date: 2019-03-23T19:57:46-04:00
showDate: true
---

If you're like me and run FreeBSD on a laptop (mine is a HP EliteBook x360 1030
G2), one issue you may encounter is the fact that switching between speakers
and headphones isn't automatic. This isn't a HP-specific problem as I also know
this impacts Dell laptops, and probably impacts other laptop brands as well.

However, not all hope is lost. FreeBSD allows you to specify your audio device
in
[`device.hints`](https://www.freebsd.org/cgi/man.cgi?query=device.hints&apropos=0&sektion=5&manpath=FreeBSD+12.0-RELEASE&arch=default&format=html)
to make automatic switchover between the integrated speakers and 3.5mm jack
possible.

On laptops where the switchover isn't automatic, you often have multiple
[`pcm`](https://www.freebsd.org/cgi/man.cgi?query=pcm&apropos=0&sektion=4&manpath=FreeBSD+12.0-RELEASE&arch=default&format=html)
devices. On my EliteBook, these `pcm` devices exist (via `dmesg | grep pcm`):

    pcm0: <Conexant (0x2008) (Analog)> at nid 23 and 26 on hdaa0
    pcm1: <Conexant (0x2008) (Right Analog Headphones)> at nid 22 on hdaa0
    pcm2: <Intel Kabylake (HDMI/DP 8ch)> at nid 3 on hdaa1

While I'm no expert on FreeBSD's audio subsystem or an audio engineer (the best
I know about audio is really only a bit more than "Beats by Dr. Dre suck"),
from what I understand, `hdaaX` is the system's HD "audio codec" (meaning audio
chip `X`) and `pcmY` is FreeBSD's audio instance (meaning instance `Y` of the
`pcm` subsystem).

Here, `hdaa0` is the audio codec for the integrated Conexant audio used by the
headphones and speakers, and `hdaa1` is the audio codec used by HDMI.

The issue which arises is that with one `hdaa` device, you have two `pcm`
devices for your integrated audio, and FreeBSD doesn't automatically configure
switching between this. To enable this switching, you need one line in
`/boot/device.hints` for your headphones:

    hint.hdac.X.cad0.nidYY.config="as=1 seq=15 device=Headphones"

Where `X` is the number assigned to the `hdaa` associated with your
"Headphones" `dmesg` line, and `YY` is the `nid` number assigned from the same
line.

You also need one line in `device.hints` for your speakers:

    hint.hdac.X.cad0.nidZZ.config="as=2 seq=0"

Where `X` is the number assigned to the `hdaa` associated with your
"Analog" `dmesg` line, and `ZZ` is the `nid` number assigned from the same
line.

Keep in mind that your system may use different terms for "Headphones" and
"Analog". Also, if you have multiple `nid` values, use one. If that doesn't
work, try the other one.

Using the output for my system, my `device.hints` has the following new lines:

    hint.hdac.0.cad0.nid26.config="as=1 seq=15 device=Headphones"
    hint.hdac.0.cad0.nid22.config="as=2 seq=0"

**Note:** Don't just copy and paste my values, your system may be different!

After entering the following lines, you should now reboot.

Once you rebooted, try playing something with audio (e.g. YouTube) with and
without headphones plugged in. This should work, as it works for me with the HP
EliteBook x360 1030 G2 running FreeBSD 12.0 mentioned above along with AKG
Y50BT headphones in wired mode.
