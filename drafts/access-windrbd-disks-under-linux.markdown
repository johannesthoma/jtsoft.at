---
layout: page
title: Accessing WinDRBD disks under Linux
permalink: /mssql-server/
nav_order: 23
parent: Howtos
---

# [](#header-1) Accessing Linux files from Windows

Set disk offline
Make resource Secondary on Windows
Make resource Primary on Linux

kpartx -a /dev/drbd<n>

mkdir mnt

mount /dev/mapper/drbd<n>p<m> mnt

example:
mount /dev/mapper/drbd50p4 mnt

example: copy windrbd driver:

cp windrbd-hardcoded-url.sys mnt/Windows/System32/drivers/windrbd.sys

sudo umount mnt
sudo kpartx -d /dev/drbd<n>
Make resource Secondary on Linux
Make resource Primary on Windows
