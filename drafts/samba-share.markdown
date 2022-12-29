---
layout: page
title: Accessing Linux files from Windows
permalink: /mssql-server/
nav_order: 23
parent: Howtos
---

# [](#header-1) Accessing Linux files from Windows

Highly insecure (write access for guest user)

vi /etc/samba/smb.conf
below map to guest

   # Guest am I
       guest account = johannes

Create shares with nautilus

Then with net command:

net use Y: \\\\ip-address\\sharename

net use Y: /delete
net use
	(to list)

