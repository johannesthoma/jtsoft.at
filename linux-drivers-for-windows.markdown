---
layout: page
title: Linux drivers for Windows
permalink: /linux-drivers-for-windows/
nav_order: 4 
parent: Projects
---

# [](#header-1)Linux drivers for Windows

Linux drivers for Windows is a project that aims to port
selected Linux drivers (like DRBD, device mapper or selected
file systems) from the original Linux kernel sources to the Windows NT
kernel. This is done by a technique we call cross kernel
development.

## Cross kernel development

In cross kernel development, one ports kernel mode drivers from one OS kernel (for example, Linux) to another OS kernel (for example, Windows), ideally without changing the source code of the driver itself. This is accomplished by a generic compatibility layer that maps the kernel APIs that the driver would expect (for example, spinlock) to the API of the target kernel (in that case KeAcqireSpinlock). The result of compiling and linking the driver with the compatibility layer is a single driver module that is directly loadable as a native driver into the target OS (in the case of Windows a .sys file).

## Current state (August 2025)

This project originated in WinDRBD which is a port of the LINBIT
DRBD driver from Linux to Windows.

Recently the 1.2.0 release of WinDRBD was released. Unilike WinDRBD 1.1
and before this release is compiled with the GNU toolchain (gcc, ...)
instead of the Microsoft Visual C compiler. This allows for tieing
the code more closely to the Linux kernel since no patches are required
(the Linux kernel uses a lot of extensions to the C language not supported
by MS VC).

## Next steps

The next step would be to integrate the source code in the Linux kernel
tree as a separate architecture.

To the linux kernel, the framework will be maintained as a (virtual) architecture (somewhat similar to um linux). Consequently, for building within the source tree you need to specify

    make ARCH=Windows 

There also will be subarchitectures for x86_64 Windows and for i386 Windows/ReactOS (and maybe also for Windows on ARM). 

## Disclaimer: What it is NOT

This is not Windows Subsystem for Linux (WSL).
Instead it is a framework for compiling Linux drivers as
native Windows drivers (without a virtual machine inbetween).
That means that software running on the Windows host (not in
a virtual machine as with WSL) can benefit from features of
a driver built with this framework. For example a database
running on Windows can be made highly available with WinDRBD,
the Linux DRBD driver for Windows.

It is also not a way to automatically compile all linux drivers for Windows. Instead the linux drivers communicate with the Windows Kernel via so called cross kernel interfaces. There are interfaces for example for block devices (in both directions) and networking (linux calls Windows only). Others like interrupt handlers (windows calls linux) for example are currently missing. 

