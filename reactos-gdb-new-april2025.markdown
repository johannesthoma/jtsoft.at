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

It will be a file named `RosBE-Unix-2.2.1.tar.bz2`. Always
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

To install libvirt and qemu on a Ubuntu (or derivate) machine,
do:

    sudo apt install libvirt-bin
    sudo apt install libvirt-daemon
    sudo apt install qemu
    sudo apt install libvirt-clients
    sudo apt install virtinst

To create a virtual machine, you can use virt-install
as follows (you may want to adjust some parameters):

    virt-install --name reactos --vcpus 1 --ram 4096 --cdrom ./bootcd.iso --disk size=40 --graphics vnc,port=5970,listen=0.0.0.0 --arch i386

Note that it is important to specify i386 as architecture, else
debugging will not work.

Use this inside the output-gdb directory (where you built ReactOS).

## Step 5: Install ReactOS

With a VNC viewer of your choice connect to the graphical
console if the new VM. The IP address should be the one
of the Linux machine where the VM is running on and the
port is the one specified in the virt-install call.

Example would be:

    vncviewer 10.43.224.40:5970

You can accept all default values (including using the
whole harddisk for the installation). The install process
is very straight forward, so I won't go into more details
here.

To (re-)start the VM use (reactos is the name of the VM):

    virsh start reactos

## Step 6: Prepare VM for use with gdb

To configure qemu's gdbserver support, you need to edit
the XML configuration of the virtual machine. Run:

    virsh edit reactos

and add:

    xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'

to the first line, so it reads something like:

    <domain type='kvm' xmlns:qemu='http://libvirt.org/schemas/domain/qemu/1.0'>

Then add command line parameters for starting qemu with gdb
server (at the end of the file, but before the closing domain
tag:

    <qemu:commandline>
       <qemu:arg value='-gdb'/>
       <qemu:arg value='tcp::2000'/>
    </qemu:commandline>

Also now is a good time to double check the qemu emulator used:
this should be:

    <emulator>/usr/bin/qemu-system-i386</emulator>

For most changes it is necessary to restart the virtual
machine (with:

    virsh shutdown reactos
    virsh start reactos

## Step 7: Attach gdb to a running ReactOS VM.

For gdb, make sure you have installed version 13.2
or above. Else file names of the source files might
be wrong.

I had to build gdb from source which is straight forward
but out of scope of this document.

Before starting gdb, cd to the symbols subdirectory
inside the output-gdb directory. This is where the
dll's, drivers (sys) and the ntoskrnl.exe with debug
symbols are placed.

Once gdb is started, you can attach to the VM with:

    target remote localhost:2000

where 2000 is the port specified in the qemu command
line tag above.

## Step 8: Load the symbols

For finding the hexadecimal offsets of the various
sections of the ntoskrnl.exe use objdump:

    objdump -h ntoskrnl.exe

It should output something like:

    ntoskrnl.exe:     file format pei-i386
    
    Sections:
    Idx Name          Size      VMA       LMA       File off  Algn
      0 .text         001b1ed0  00401000  00401000  00000000  2**4
                      ALLOC, LOAD, READONLY, CODE
      1 PAGE          000046a8  005b3000  005b3000  00000000  2**2
                      ALLOC, LOAD, READONLY, CODE
      2 .data         00004034  005b8000  005b8000  00000000  2**5
                      ALLOC, LOAD, DATA
      ...

and so on. On i368 systems you need to add 0x80000000 for
the ntoskrnl, so the beginning of the .text section would
be 0x80401000 in the above example.

Here is how the symbols are loaded on *my* system (you need
to adjust the offsets):

    add-symbol-file ../../output-gdb/symbols/ntoskrnl.exe 0x80401000 -s .bss 0x8061c000 -s .data 0x805b8000 -s .edata 80645000

To verify if the .text offset is correct you can disassemble
a function:

     (gdb) x/i 'KeAcquireSpinLockAtDpcLevel@4'
        0x8049fc60 <KeAcquireSpinLockAtDpcLevel@4>:	push   %ebp

Note that the single quotes are mandatory if there is an '@'
in the function name. The assembly should show

    push   %ebp

To load drivers you need to find the offsets where the driver
is loaded. You can do so with scanning the kernel log (which
can be seen with

    virsh console reactos

when the system is booting) or you can use following gdb macro:

    define list-modules
      set $m = (struct _LDR_DATA_TABLE_ENTRY*)PsLoadedModuleList->Flink
      while &$m->InLoadOrderLinks != &PsLoadedModuleList
        p $m->BaseDllName.Buffer
        p $m->DllBase
        set $m = (struct _LDR_DATA_TABLE_ENTRY*)$m->InLoadOrderLinks.Flink
      end
    end

The output looks somehow like:

    $1 = (PWSTR) 0xb7a61eb8 L"ntoskrnl.exe"
    $2 = (PVOID) 0x80400000
    $3 = (PWSTR) 0xb7a61e50 L"hal.dll"
    $4 = (PVOID) 0x80974000
    ....

and so on. To load you need to add 0x1000 (4KB) to the value
printed after the filename (the PVOID value) so if you have:

    $65 = (PWSTR) 0xb72c3b10 L"windrbd.sys"
    $66 = (PVOID) 0xf5fd8000

the command to load the symbols for the windrbd.sys driver
would be:

    add-symbol-file windrbd.sys 0xf5fd9000

Now if you backtrace, you should see kernel symbols (if
the VM is currently executing in kernel mode) in the backtrace.

To backtrace simply do:

    bt

## Step 9: You're done :)

You now can start your debug session. To break on blue screen
for example you can enter:

    break RtlpBreakWithStatusInstruction@0

You can also use C expressions like the following to enable
TCPIP logging:

     p *(int*)(((char*)(&Kd_TCPIP_Mask))+0x80000000) = 0x800

I've put a collection of gdb macros ('real' gdb macros, not
python) in a github repo:

    https://github.com/johannesthoma/gdb-macros-for-reactos

As always, happy hacking !!!
