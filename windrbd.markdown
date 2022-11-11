---
layout: page
title: WinDRBD
permalink: /WinDRBD/
nav_order: 3
parent: Projects
---

# [](#header-1)WinDRBD - DRBD For Microsoft Windows

WinDRBD is a Windows kernel driver that provides (almost)
the same functionality as DRBD under Linux. It is based
on DRBD 9 and uses the original DRBD source code wrapped
in a compatility layer that makes it possible to run
DRBD within Windows NT kernels.

By using WinDRBD one can implement high availablility
(HA) on Windows servers or even mix Linux and Windows
servers. In such a scenario, if a node fails another
node can take over the services (like databases, web
servers and so on). This makes it possible to achieve
almost 100% availability for internet servers.

![](../../assets/images/WinDRBD_Connections-1024x645.png)

As the image outlines, WinDRBD translates DRBD API calls
(which are Linux kernel specific) to Windows NT kernel
API calls. This is done for storage, networking and
user node level interfaces (resource configuration via
netlink and providing storage interface for the WinDRBD
resources).

Also shown is that WinDRBD can talk seamlessly to Linux
DRBD which makes mixed Linux/Windows setups with up to
32 nodes possible.
