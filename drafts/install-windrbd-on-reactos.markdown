---
layout: page
title: TODO
permalink: /TODO/
nav_order: 3
parent: Howtos
---

# [](#header-1)TODO

Run installer

Must patch registry to create the Virtual Bus Device
	(so that drbdadm primary works)

(reg files are on jtsoft.at in the home directory)
	Make sure that device numbers match...or so

VM is the reactos-debug-may2023 (where it is already
exiting). VNC port is 5959.

There also should be a newer VM based on reactos master...
	Found it.

And network requires netio.sys which I need to implement.

Hi @hbelusca there are surprisingly only a few differences, the 
most notable is that there is no networking support yet
because WinDRBD uses the NETIO.SYS interface ('Windows
kernel sockets') to access the network. Other changes
area things like missing IoCreateDeviceSecure(),
shared spinlocks (in Linux they are called read/write locks)
no no-execute pages support. Also currently the drbd-utils
(drbdadm and the like) have to be compiled on ReactOS since
there is no cross compiler for this old version of CygWin.
And there are some changes to the installer because ReactOS
doesn't have a driver store support (pnputils are missing)
At the moment also the bus device fails to find a driver
but this is probably a bug in WinDRBD (it used to work
with ReactOS 0.4.14). So minor things one can live without :smile:


