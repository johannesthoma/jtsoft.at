---
layout: page
title: Debugging ReactOS with gdb
permalink: /reactos-gdb/
nav_order: 3
parent: Howtos
---

# [](#header-1)TODO

The GNU DeBugger (gdb) is a very powerful debugger which allows
to debug programs at source level. It allows evaluation of C
statements (in contrast to the Windows Kernel debugger)  and
many more things.

We will show how to setup a virtual machine on top of a Linux
host using libvirt (virsh and virt-manager).

To debug ReactOS one has to configure it for GDB first and
then build it. This assumes that you already have the
ReactOS build environment installed on your Linux box.

First, clone the ReactOS source with this command:

    git clone https://github.com/reactos/reactos.git
    cd reactos

Then (optionally) check out the branch you want to debug:

    git checkout releases/0.4.14

The master branch is the development branch (as I understood it)
and is not as stable as the release branches (which also are
worked on).

To build ReactOS you have to start the React OS build environment:

    /path/to/rosbe-bin/RosBE.sh

Then create an output directory:

    mkdir output-gdb
    cd output-gdb
# wont boot when _WINKD_ is given:

Then configure ReactOS with GDB (but without WINKD) as follows:

    /path/to/reactos/configure.sh -D_WINKD_:BOOL=FALSE -DGDB:BOOL=TRUE -DSEPARATE_DBG:BOOL=TRUE -DKDBG:BOOL=FALSE

and build it:

    ninja bootcd

Note the absence of ``_WINDK_`` - ReactOS would wait for the Windows
kernel debugger and not boot.

<!-- sudo apt install rosbe-unix

/usr/RosBE/RosBE.sh -->

---

add xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0' to first line
then add
  <qemu:commandline>
    <qemu:arg value='-s'/>
    <qemu:arg value='-S'/>
  </qemu:commandline>
xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'at the end (before /domain after /devices)

---
building gdb (does not work):
sudo apt install libreadline-dev
	(no)

https://reactos.org/wiki/GDB

building RoSBE:

Download from https://reactos.org/wiki/Build_Environment

sudo apt install texinfo
sudo apt install zlib1g-dev
run the RoSBE build script
---
sudo apt install mingw-w64
sudo apt install gdb-mingw-w64
	(still does not work)

New RoSBE (self built)
~/rosbe-bin/RosBE.sh


--
cd reactos-2023/reactos/output-may-2023-2
---
i686-w64-mingw32-gdb
(also works with normal gdb?)

file symbols/ntoskrnl.exe
add-symbol-file ./symbols/ntoskrnl.exe 0x80801000

offset is start of .text section (use i686-w64-mingw32-objdump -h ./ntoskrnl.exe to find it there the VMA address) and then add 0x80000000

TODO: this is needed? 
set solib-search-path ./symbols

This is not needed since we 
# set architecture i386:x86-64
target remote 10.43.224.40:1234

stops in ?? use

bt

to see backtrace (with correct symbols)

then fin

and (for example)

list '@KiIdleLoop@0'

to name functions containing a @ put the function name into single
quotes: like:

p '@KiIdleLoop@0'

----
Use qemu-system-i386

virsh edit <domain>, then
Change:
<type arch='i686' machine='pc-i440fx-bionic'>hvm</type>
and
<emulator>/usr/bin/qemu-system-i386</emulator>

---

Debug symbols are in 
output-xxxx/symbols

Take ntoskrnl.exe from there.
