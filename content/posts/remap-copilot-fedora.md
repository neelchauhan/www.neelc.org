---
title: "Remapping the Copilot Key to Right Ctrl on Fedora 43"
date: 2025-11-16T21:15:00-05:00
showDate: true
---

If you own a modern non-Mac system, chances are there's a Copilot key on it.

As a Fedora user, there's absolutely no way I'd use Copilot. And even if I
could, I wouldn't: I've *worked* at Microsoft yet barely used AI.

However, I do want a working Right Ctrl key for VirtualBox. How do I do that?

# Step 1: Enable the Copr repository

You'll need to import keyd from Copr and install it:

    sudo dnf copr enable alternateved/keyd
    sudo dnf install keyd

# Step 2: Add the Configuration File

You'll also need a configuration file. This lets `keyd` know to remap the
Copilot key.

First, make the `/etc/keyd` directory:

    mkdir /etc/keyd

Now, add the following in `/etc/keyd/default.conf`:

    [ids]
    *
    [main]
    leftshift+leftmeta+f23 = rightcontrol

# Step 3: Enable `keyd`:

You can enable `keyd` from `systemctl`:

    systemctl enable --now keyd

# Concluding Remarks

It seems Microsoft hasn't learned their lesson from Windows 8: forcing your
product does not make it a smashing hit.

Regardless, if you're using Linux and obviously can't use a Copilot key, you
might very well get Right Ctrl back.
