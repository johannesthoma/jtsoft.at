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
host using libvirt (virsh and virt-install) and how to use
gdb to debug the ReactOS inside this VM.

Note that there are two ways to debug ReactOS:

 * via the gdb support built into ReactOS.
 * via the gdb support built into qemu

Since qemu offers more features we will use this approach
in this guide. If you install ReactOS on physical hardware
(for example when you write a driver for a new device)
then you need to debug it via the gdb support built into
ReactOS via a serial cable.

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

Answer the questions (like installation directory)
We assume that the installation directory is

    /home/johannes/reactos/RosBE

in this document.

## Step 2: Check out current ReactOS source code

Get the current source code. Development is made against
the master branch there are also other branches starting
with ``releases`` (such as releases/0.4.14). We will 
now use the master branch since we want to do some
debugging and development.

So here we go:

    git clone https://github.com/reactos/reactos.git

The master branch should be checked out initially so
no need to switch branches now.

## Step 3: Configure and build ReactOS

To build ReactOS you first need to put your shell into
RoSBE mode. To do so, run 

    /home/johannes/reactos/RosBE/RosBE.sh 

ReactOS is built in separate directories. By doing so
one can have several different builds and the source
directories are kept clean.

When debugging via the qemu gdb support one has only to
tell the build system that binaries containing debug
symbols should be used. You can use any directory
name instead

    mkdir output-gdb
    cd output-gdb
    ../configure.sh -DSEPARATE_DBG:BOOL=TRUE
    ninja bootcd

When debugging with the built in ReactOS gdb support
you need to enable it: configure it with

    ../configure.sh -DGDB:BOOL=TRUE -DSEPARATE_DBG:BOOL=TRUE

## Step 4: Create a virtual machine using virt install

sudo apt install libvirt-bin
sudo apt install libvirt-daemon
sudo apt install qemu
sudo apt install libvirt-clients
sudo apt install virtinst

To create a virtual machine, you can use virt-install
as follows (you may want to adjust some parameters):

    virt-install --name reactos --vcpus 1 --ram 4096 --cdrom ./bootcd.iso --disk size=40 --graphics vnc,port=5970,listen=0.0.0.0 --arch i386

TODO: no autostart after install  ...
TODO: and leave the cdrom attached

Use this inside the output directory.

## Step 5: Install ReactOS

With a VNC viewer of your choice connect to the graphical
console if the new VM. The IP address should be the one
of the Linux machine where the VM is running on and the
port is the one specified in the virt-install call.

Example would be:

# TODO: check this
    vncviewer 10.43.224.40:5970


Press Enter ....

Then do 

    virsh start <vm-name>
(not necessary)

## Step 6: Prepare VM for use with gdb

add xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0' to first line
then add
  <qemu:commandline>
    <qemu:arg value='-gdb'/>
    <qemu:arg value='tcp::2000'/>
  </qemu:commandline>
  <emulator>/usr/bin/qemu-system-i386</emulator>

## Step 7: Attach gdb to a running ReactOS VM.


## Step 8: Load the symbols


## Step 9: You're done :)
---

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
	No - debug info source files are wrong (in both cases)

Update: Must use a recent gdb (like 13.2 and up)
	(I built it from sources)
	Else Source files will be interpreted wrong
	(so err.h instead of drbd_nl.c - however the line
	numbers are correct but not the files, so list
	and friends (display source on single stepping)
	do not work).

On 10.43.224.40:
 ~/gdb-build/gdb-13.2/gdb/gdb

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



when drivers are loaded a message
	Loading ..../mydriver.sys at F283C000 with 1476 pages
appears on the console.

.text address is 0xF283C000 + offset of .text section (objdump -h mydriver.sys)
rounded up to the next page size (usually 4k = 0x1000).

So to add symbols for a driver in our case (offset is 0x400) do

add-symbol-file /home/johannes/windrbd.sys 0xf283d000

Note that remove-symbol-file /home/johannes/windrbd.sys
only removes the change made by the oldest add-symbol-file
command. So to remove all add-symbol-file instances you
have to repeat the remove-symbol-file /home/johannes/windrbd.sys
until it says No symbol file found

remove-symbol-file takes absolute path
to clear all symbol file entries (do this after resetting the target) use
symbol-file

break on blue screen:
break RtlpBreakWithStatusInstruction@0
or better: (update: no because there is the internal kernel debugger))
break KeBugCheckEx@20

RtlpBreakWithStatusInstruction@0

----
Use qemu-system-i386

virsh edit <domain>, then
Change:
<type arch='i686' machine='pc-i440fx-bionic'>hvm</type>
and
<emulator>/usr/bin/qemu-system-i386</emulator>
inside <devices>

---

Debug symbols are in 
output-xxxx/symbols

Take ntoskrnl.exe from there.

----

cd ~/reactos-symbols
i686-w64-mingw32-13.2-gdb
target remote localhost:2001
add-symbol-file ntoskrnl.exe 0x80401000
add-symbol-file windrbd.sys 0xF512C000
add-symbol-file windrbd.sys 0xF50D2000

break before calling driver init function (but after driver is loaded):
break ../ntoskrnl/io/iomgr/driver.c:631

----

Use gdb 13.2 with newest binutils: a bug that caused 
	wrong source file names to be picked has been
	fixed therein.

Configure with:
	./configure --target=i686-w64-mingw32 --program-prefix=i686-w64-mingw32-13.2-

	or so --prefix is something like /usr/local I think

-----

Enable/disable logging:

     p *(int*)(((char*)(&Kd_TCPIP_Mask))+0x80000000) = 0x800

----

Context switch: on i368 there is a task state segment (TSS):
It is printed by the built in debugger of ReactOS (type bt
in the ReactOS window)

    p *(struct _KTSS*)0x803FA000

set 3 registers:

    p/x $eip = ((struct _KTSS*)0x803FA000)->Eip
    p/x $esp = ((struct _KTSS*)0x803FA000)->Esp
    p/x $ebp = ((struct _KTSS*)0x803FA000)->Ebp

or also the tss command

kdb:> tss                                                                       
Current TSS 0x0028 is at 0x803FC000.                                            

----
info registers
info all-registers
