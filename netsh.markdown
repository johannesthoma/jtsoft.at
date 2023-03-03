---
layout: page
title: Some useful netsh commands
permalink: /netsh/
nav_order: 28
parent: Howtos
---

# [](#header-1)Some useful netsh commands

What the ``ip`` command is for Linux is the ``netsh`` command
for Windows. It allows configuring of network interfaces,
query status and even control the Windows firewall. I will
present some useful commands that I use frequently

## IP address related commands

To show addresses and more parameters of all network interfaces, do:

    netsh interface ipv4 show address

To add an address to an interface:

    netsh interface ipv4 add address "Ethernet 4" 10.43.224.99

This can be useful to set a cluster IP that always points to
the active node.

To delete it, do

    netsh interface ipv4 add address "Ethernet 4" 10.43.224.99

And very useful in case you have DHCP problems:

    ipconfig /renew

Will work only if you have DHCP configured.

## Firewall related commands

To turn off firewall completely for all profiles:

    netsh advfirewall set allprofiles state off

To check the firewall status:

    netsh advfirewall show allprofiles

To enable IPv4 pings:

    netsh advfirewall firewall add rule name="Allow IPv4 pings" protocol=icmpv4:8,any dir=in action=allow

To allow a TCP/IP port:

    netsh advfirewall firewall add rule name=test1 protocol=tcp dir=in localport=5000 action=allow

To enable/disable an existing firewall rule:

    netsh advfirewall firewall set rule name="LINSTOR Port 7003" new enable=yes
    netsh advfirewall firewall set rule name="LINSTOR Port 7003" new enable=no

To dump firewall rule:

    netsh advfirewall firewall show rule name="LINSTOR Port 7003"

That's it for now, I will add more useful netsh commands as I
learn them :)
