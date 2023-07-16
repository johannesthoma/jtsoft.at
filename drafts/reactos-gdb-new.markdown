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
host using libvirt (virsh and virt-manager) and how to use
gdb to debug the ReactOS inside this VM.

## Step 1: Get the ReactOS build environment (RosBE) working.

Download RosBE from following location:

    https://reactos.org/wiki/Build_Environment

It will be a file named `RosBE-Unix-2.2.1.tar.bz2`. Alway
pick the latest version.

Untar it using

    tar jxf RosBE-Unix-2.2.1.tar.bz2

You might have to change the version number.

Then inside the extracted directory run RosBE-Builder.sh as follows:

    cd RosBE-Unix-2.2.1
    ./RosBE-Builder.sh

Answer the questions
TODO: do again and check

## Step 2: 

Get the current source code. Development is made against
the master branch there are also other branches starting
with ``releases`` (such as releases/0.4.14). We will 
now use the master branch since we want to do some
debugging and development.

So here we go:

    git clone https://github.com/reactos/reactos.git

The master branch should be checked out initially so
no need to switch branches now.

# cd output-i686-mingw-newbuild
mkdir output-may-2023-2
cd output-may-2023-2
# wont boot when _WINKD_ is given:
# /path/to/reactos/configure.sh -D_WINKD_:BOOL=FALSE -DGDB:BOOL=TRUE -DSEPARATE_DBG:BOOL=TRUE -DKDBG:BOOL=FALSE
update: do not

ninja bootcd
sudo apt install rosbe-unix

/usr/RosBE/RosBE.sh

---

add xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0' to first line
then add
  <qemu:commandline>
    <qemu:arg value='-s'/>
    <qemu:arg value='-S'/>
  </qemu:commandline>
xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'at the end (before /domain after /devices)

---
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
