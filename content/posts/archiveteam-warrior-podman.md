---
title: "Running ArchiveTeam Warrior in Podman on Rocky Linux 9"
date: 2024-07-08T11:50:00-04:00
showDate: true
---

I don't remember where I heard about ArchiveTeam from, but when I did learn
about it I knew I wanted to join in.

I have run Tor relays for over a decade now but always wanted to participate in
other volunteer-run services as well. I always felt good when my home servers
serve more people than just me. I run an I2P node too, but CPU-and-GPU-heavy
tasks like Folding@Home are out usually due to excessive power consumption
and noise.

The problem with ArchiveTeam is, the instructions
[online aren't](https://wiki.archiveteam.org/index.php/ArchiveTeam_Warrior)
[very good](https://wiki.archiveteam.org/index.php/Running_Archive_Team_Projects_with_Docker)
meaning my ArchiveTeam always gets reset upon reboot which always sucks.

So I decided to take a slightly different path towards running ArchiveTeam
Warrior in Podman on Rocky Linux 9, meaning using quadlets so it's on 24/7.

First off, you should install Podman if you haven't already:

    dnf install -y podman

If you're running another RHEL-like system (CentOS Stream, AlmaLinux, et al.)
or Fedora the command should be the same. If you're running a non-Red Hat-based
distro you might have to use `apt`, `pacman`, `zypper`, et al.

Next, edit the `/etc/containers/systemd/archiveteam-warrior.container` file and
insert this:

    # archiveteam-warrior.container
    [Container]
    AutoUpdate=registry
    ContainerName=archiveteam-warrior
    Image=atdr.meo.ws/archiveteam/warrior-dockerfile
    PublishPort=8001:8001
    Volume=archiveteam-warrior-projects:/home/warrior/projects
    
    [Service]
    Restart=always
    
    [Install]
    WantedBy=multi-user.target default.target

Subsequently, run:

    systemctl daemon-reload

And:

    systemctl start archiveteam-warrior

And ArchiveTeam Warrior should be started.

The good thing is Podman takes care of the `firewalld` rules (at least it did
for me). To top that off systemd automatically starts Warrior at boot (assuming
you have `WantedBy=multi-user.target default.target`).
