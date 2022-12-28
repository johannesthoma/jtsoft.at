---
layout: page
title: Short introduction to EFI shell
permalink: /efi-shell-intro/
nav_order: 25
parent: Howtos
---

# [](#header-1) Short introduction to EFI shell.

When installing Windows onto a WinDRBD disk one has to boot
via UEFI. Most modern system support this so it should not
be a strict restriction. When installing onto a system
that is booted via the legacy PC BIOS the Windows setup
utility will refuse to install on top of a WinDRBD device
because it thinks the BIOS cannot boot from the target
disk (which is not true - it can boot via iPXE).

However, booting from UEFI needs a special iPXE image
compiled for UEFI and with WinDRBD support. This image
comes precompiled with modern WinDRBD distributions
(TODO: starting from 1.1.5).

This guide explains what to type into the EFI shell to
load an iPXE image and boot from it. It is also meant
as a generic introduction for those who (like me) never
worked with an EFI shell. EFI shell is a bit like
cmd shell under Windows but there are significant
differences.

To list commands supported by your EFI shell type:

    help -b

The -b parameter makes the output stop at every page
(just like piping into less) so you can view all pages.

Then to access local file systems you need to select
a file system and then change into a directory containing
the file. First, list file systems with the map command:

    map

It should show something like

    FS0: Alias()...
	PciRoot(0x0)/Pci ...

The FS0 is the interesting part. It is like a Windows drive
letter but with more than one letter. So, to select a file
system enter:

    FS0:

(or FS1:, ...). Then optionally you can cd into the directory
on the filesystem. Example:

    cd \EFI\BOOT

To list the files do:

    dir -b

(again -b is to make the output stop at every page).

Then to boot from a .efi file just type the name of the file,
as in:

    ipxe.efi

That's it.
