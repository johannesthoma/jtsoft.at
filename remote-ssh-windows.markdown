---
layout: page
title: Configure ssh access on Windows
permalink: /windows-ssh/
nav_order: 23
parent: Howtos
---

# [](#header-1) Configure SecureShell (ssh) access on Windows

Once you have installed [Cygwin](https://www.cygwin.org)
on your ReactOS/Windows box it is just a few commands
configuring remote terminal access via OpenSSH. Just
like with most UNIX/Linux boxes you can log in remotely
and get a shell prompt with the usual GNU tools. In
addition on a CygWin shell also most Windows command
line tools work (in contrast to the WSL2 shell) and
you can also start a cmd shell or a power shell prompt.

So, here are the steps:

  * Open a CygWin Terminal
  * Run the ssh-host-config tool to configure the host key
    plus some other things:

        ssh-host-config

    It will prompt you for some options, you can accept all
    defaults.
  * Open port 22 (the ssh TCP port) in the Windows firewall:

        netsh advfirewall firewall add rule name='Enable sshd' protocol=tcp localport=22 dir=in action=allow

    Note that this is a generic way to open a port on Windows.
    Just replace 22 with the desired port.
  * When done, you can start the SSH daemon (called service
    under Windows) with following command:

        sc start cygsshd

    Note that the SSH daemon is started automatically on
    booting.

You have successfully configured ssh access for your Windows
box. To log in run

    ssh Administrator@<your-windows-ip-here>

You can use your Windows Administrator (or user) account
password to log into your Windows box.

If you don't know your Windows IP run

    ipconfig

to look it up.

That's it :) you have configured SSH on your Windows box
and can use it (almost) like a real UNIX/Linux box.

Note that CygWin export a /cygdrive virtual directory
to map drive letters to the CygWin root tree. So to
list files in C: type:

    ls /cygdrive/c

Happy hacking :)
