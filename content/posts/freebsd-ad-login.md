---
title: "Setting up Login for Windows Server/Samba Active Directory on FreeBSD"
date: 2022-04-10T21:00:00-07:00
showDate: true
---

**Disclaimer:** I work at Microsoft, but not on Windows Server or Active
Directory.

Recently, in my homelab, I decided to enable a *single sign-on* using Active
Directory on my two servers. Despite my employment, my homelab is very
FreeBSD-centric, due to me having used it for 9+ years versus 2+ at my current
job.

While I could use OpenLDAP which is technically more Unix-centered than
Windows-centered, I hate OpenLDAP. I find it easier to use Active Directory,
whether Windows or Samba, as I am currently using a Samba 4.13 Domain
Controller on FreeBSD.

Going on, you'll want to set your hostname in the format of
`SYSTEM_NAME.AD_DOMAIN_NAME`. This can be done with:

    sysrc hostname="SYSTEM_NAME.AD_DOMAIN_NAME"

The all-caps fields should be self-explanatory.

You'll also want a static IP, or an entry for your host in the DNS/DHCP. If you
go the static IP route, set one like:

    sysrc ifconfig_INTNAME="inet IP_ADDRESS netmask SUBNET_MASK"

Again, the all-caps fields should be self-explanatory.

If you have a static IP, you need an entry for your system in `/etc/hosts` like:

    IP_ADDRESS	SYSTEM_NAME.AD_DOMAIN_NAME AD_DOMAIN_NAME

You'll also want to make sure the time is accurate on both the domain
controller and system joining the domain.

Then install Samba and `pam_mkhomedir`:

    # pkg install samba413 pam_mkhomedir

Note: At the time of writing, the latest Samba package is `samba413`. This
could change in the future.

Then, you'll want to set up `/usr/local/etc/smb4.conf`:

    [global]
    	netbios name = SYSTEM_DOMAIN_NAME
    	realm = AD_DOMAIN_NAME
    	workgroup = NETBIOS_NAME
    	security = ADS
    	winbind enum groups = Yes
    	winbind enum users = Yes
    	winbind nss info = rfc2307
    	idmap config *:range = 10000-99999
    	idmap config * : backend = tdb
    	template shell = /bin/sh
    	template homedir = /home/%U

Like always, the all-caps fields should be self-explanatory.

If you don't have a `/home` or `/usr/home` directory, make one now as `root`:

    # mkdir /home

Then, you should test the Samba configuration, just to be safe:

    # testparm

Now, join the domain and enter the domain's "Administrator" password:

    # net ads join -U administrator

The AD DC should have the FreeBSD box you are joining registered.

Now, enable `samba_server` and `winbindd` as follows:

    # sysrc samba_server_enable="YES"
    # sysrc winbindd_enable="YES"

And start Samba:

    # service samba_server start

Just to make sure, check that you can see the AD users and groups:

    # wbinfo -u
    # wbinfo -g

I have tested these instructions on both a Samba 4.13/FreeBSD 13.0 and Windows
Server 2022 Active Directory Domain Controller.

Next, we need to edit `/etc/nsswitch.conf`. In this file, change these two lines:

    group: files winbind
    passwd: files winbind

Now, we edit the PAM configuration. Edit `/etc/pam.d/system` to look like this:

    auth            sufficient      pam_opie.so             no_warn no_fake_prompts
    auth            requisite       pam_opieaccess.so       no_warn allow_local
    auth            sufficient      /usr/local/lib/pam_winbind.so
    #auth           sufficient      pam_krb5.so             no_warn try_first_pass
    #auth           sufficient      pam_ssh.so              no_warn try_first_pass
    auth            required        pam_unix.so             no_warn try_first_pass nullok

    # account
    account         sufficient      /usr/local/lib/pam_winbind.so
    #account        required        pam_krb5.so
    account         required        pam_login_access.so
    account         required        pam_unix.so
    
    # session
    session         required        /usr/local/lib/pam_mkhomedir.so
    #session        optional        pam_ssh.so              want_agent
    session         required        pam_lastlog.so          no_fail
    
    # password
    password        sufficient      /usr/local/lib/pam_winbind.so
    #password       sufficient      pam_krb5.so             no_warn try_first_pass
    password        required        pam_unix.so             no_warn try_first_pass

Then change `/etc/pam.d/sshd` to look like this:

    auth            sufficient      pam_opie.so             no_warn no_fake_prompts
    auth            requisite       pam_opieaccess.so       no_warn allow_local
    auth            sufficient      /usr/local/lib/pam_winbind.so
    #auth           sufficient      pam_krb5.so             no_warn try_first_pass
    #auth           sufficient      pam_ssh.so              no_warn try_first_pass
    auth            required        pam_unix.so             no_warn try_first_pass
    
    # account
    account         sufficient      /usr/local/lib/pam_winbind.so
    account         required        pam_nologin.so
    #account        required        pam_krb5.so
    account         required        pam_login_access.so
    account         required        pam_unix.so
    
    # session
    session         required        /usr/local/lib/pam_mkhomedir.so
    #session        optional        pam_ssh.so              want_agent
    session         required        pam_permit.so
    
    # password
    password        sufficient      /usr/local/lib/pam_winbind.so
    #password       sufficient      pam_krb5.so             no_warn try_first_pass
    password        required        pam_unix.so             no_warn try_first_pass

You can also add these to other `/etc/pam.d` files if you desire AD login
support from other parts of the system (e.g. X11, VPN, etc.).

Reboot, and you should be set.

If you desire additional security, you could:

 * In `sshd`, add the `AllowGroups AD_DOMAIN_NAME\GROUP_NAME` to the `sshd_config`

 * If you wish for some users to have root access, add `%AD_DOMAIN_NAME\\GROUP_NAME ALL=(ALL) NOPASSWD: ALL` to the `sudoers` file (note the double slash)

Source: [FreeBSD: Setup Samba as an AD Domain Member](https://blog.andreev.it/?p=2676)
