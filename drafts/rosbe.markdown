---
layout: page
title: TODO
permalink: /TODO/
nav_order: 3
parent: Howtos
---

# [](#header-1)TODO

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

git clone https://github.com/reactos/reactos.git

# cd output-i686-mingw-newbuild
mkdir output-may-2023-2
cd output-may-2023-2
# wont boot when _WINKD_ is given:
# works with reactos 0.4.14 but not with master:
# /path/to/reactos/configure.sh -D_WINKD_:BOOL=FALSE -DGDB:BOOL=TRUE -DSEPARATE_DBG:BOOL=TRUE -DKDBG:BOOL=FALSE
# this works??
..//configure.sh -D_WINKD_:BOOL=TRUE -DGDB:BOOL=TRUE -DSEPARATE_DBG:BOOL=TRUE -DKDBG:BOOL=FALSE
ninja bootcd

--
cd reactos-2023/reactos/output-may-2023-2
---
i686-w64-mingw32-gdb
(also works with normal gdb?)

file symbols/ntoskrnl.exe
# add-symbol-file ./symbols/ntoskrnl.exe 0x80801000

cd symbols
add-symbol-file ./ntoskrnl.exe 0x80401000
add-symbol-file ./kdcom.dll 0x80085000

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
