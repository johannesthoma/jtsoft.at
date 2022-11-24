---
layout: page
title: Installing Windows Server 2019 on OpenNebula
permalink: /installing-windows-server/
nav_order: 24
parent: Howtos
---

# [](#header-1) Installing Windows Server 2019 on OpenNebula 

This page gives you an incomplete introduction about how to install
a Windows Server VM if you already have an OpenNebula cluster. We
need these instances for testing [WinDRBD](https://www.github.com/LINBIT/WinDRBD).

# [](#header-2) Downloading ISO images

First you need to get a ISO image for Windows Server 2019: you
can download it from [This Location](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
You have to provide some personal info, but the eMail address is not validated.
This version is valid for 180 days (I *think* from installing it the first time, but I didn't count), so be prepared to either obtain a license or re-install Windows after 180 days.

Then to achive maximum performance we will use the [virtio](https://wiki.libvirt.org/page/Virtio) interface, which bypasses the need to emulate networking / storage and other hardware. To make this work one must install the virtio driver package on the guest system (the new Windows Server 2019 instance). An ISO image with the VirtIO drivers can be downloaded from [This location](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.221-1/)

Then you need to upload both images to your OpenNebula cloud. Do this with
secure copy (TODO: to where - ask Moritz how this works) (it won't work
via the network, don't know why)

# [](#header-2) Configure VM Template

From the dashboard (menu on the left side you might have to click
the button with the three lines) select Storage / Images.

Then select the green plus sign and from there Create. Name the
Image (for example Windows Server 2019 ISO). For type select 
ReadOnly CDROM image. For the image itself select Path/URL
and type the filename (TODO: Ask Moritz about this).

Before creating the CDROM image you have to set the bus type to
IDE (for both images). To do so open the Advanced Options (you
might have to scroll down) and where it says Bus, select
Paralell ATA (IDE).

Do the above for both images (Windows ISO and VirtIO ISO).

C: Disk: create empty disk image
	Type Datablock
	persistent YES
	Format qcow2
	Bus virtio

Create VM Template
	16GB RAM
	4 Physical CPUs
	8 virtual CPUs
	Storage: With C: Disk just created and with 2 CDROMs above
	Network: Office Network (virtio...?)
		Development: Also add debug network - NIC Model e1000e (or so)
	OS & CPU:
		Boot: Boot from CDROM then from C: disk
		CPU Model: Host passthru
	Input:
		Tablet / USB (? fixes mouse ?)

Then create a VM instance from that new VM template

