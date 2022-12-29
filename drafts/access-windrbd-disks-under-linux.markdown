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

# TODO: how to find name:
mount /dev/dm-?

