---
title: "Using QEMU with the virtio-net Internal Network for Network Simulations"
date: 2025-06-28T15:50:00-04:00
showDate: true
---

For years I've primarily used x86 PCs. With that, VirtualBox had a unique
feature called the **Internal Network** that could be used to isolate VLANs.
But more recently I've been daily-driving a M3 Pro MacBook Pro on-and-off.

However, VirtualBox on Apple Silicon cannot run MikroTik CHR. While
[it does run on UTM](https://github.com/tikoci/mikropkl), UTM doesn't give me
isolated non-DHCP VLANs. Also, attempts at using "bridge" interfaces failed
miserably.

The solution? QEMU. I tested this guide on macOS 15 Sequoia but it should also
work on Linux, Windows and BSD variants.

The commands for VM 1:

    qemu-system-aarch64 \
      -machine virt \
      -cpu cortex-a57 \
      -m 1024 \
      -bios /opt/homebrew/Cellar/qemu/10.0.2_2/share/qemu/edk2-aarch64-code.fd \
      -nographic \
      -drive file=c1.img,format=raw,if=virtio \
      -device virtio-net,netdev=net1 -netdev socket,id=net1,listen=:1234

And VM 2:

    qemu-system-aarch64 \
      -machine virt \
      -cpu cortex-a57 \
      -m 1024 \
      -bios /opt/homebrew/Cellar/qemu/10.0.2_2/share/qemu/edk2-aarch64-code.fd \
      -nographic \
      -drive file=c2.img,format=raw,if=virtio \
      -device virtio-net,netdev=net1 -netdev socket,id=net1,connect=:1234

**NOTE:** These commands are specific for ARM Macs using Homebrew. Linux/BSD
systems and x86 Macs (if not emulating ARM64) *will* have different commands.

The most interesting line on these two VMs is the last line. From VM 1:

    -device virtio-net,netdev=net1 -netdev socket,id=net1,listen=:1234

This line sets up a `net1` network listening on port `1234`.

For the VM 2:

    -device virtio-net,netdev=net1 -netdev socket,id=net1,connect=:1234

This connects to VM 1 via port `1234`

You can technically repeat these lines for multiple VLANs/interfaces, but
haven't done it (yet).
