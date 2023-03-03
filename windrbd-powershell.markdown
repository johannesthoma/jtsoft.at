---
layout: page
title: How to find WinDRBD disks by minor in PowerShell
permalink: /windrbd-powershell/
nav_order: 26
parent: Howtos
---

# [](#header-1)How to find WinDRBD disks by minor in PowerShell

When preparing WinDRBD disks for use by creating a partition,
formatting it and assigning a drive letter, the DISKPART utility
does not fit the bill. The reason is that it is not possible to
predict which disk number the disk will get in the DISKPART
utility. The same is true for the partition manager in the
control panel, as well as the /dev/sd? entries in the CygWin
/dev virtual directory.

For PowerShell (and probably other programs that use Windows
Management Interface (WMI)) there exists the possibility
to filter disks by their Path property. The Path property
is filled directly by the WinDRBD driver and contains the
WinDRBD minor number (which is unique on a single host).

So, here's the PowerShell command, ready for copy&paste:

    Get-Disk | where { $_.Path -like "*#WinDRBD{0}#*" -f 42 }

All you need to do is to substitute 42 by your WinDRBD
minor number. You then can assign it to a variable:

    $mydisk = Get-Disk | where { $_.Path -like "*#WinDRBD{0}#*" -f 42 }

and for example list partitions on it:

    $mydisk | get-partition

Of course also directly:

    Get-Disk | where { $_.Path -like "*#WinDRBD{0}#*" -f 42 } | Get-Partition

Going into details:

    Get-Disk

prints some properties of all disks. To see which properties are supported,
use Get-Member:

    Get-Disk | Get-Member

You can print the values of some (or all) properties using
format-list:

    Get-Disk | format-list disknumber,path

Then the Where command can find disks within certain constraints:

    Get-Disk | where {$_.disknumber -eq 8}

The $_ is substituted by whatever Get-Disk pipes into it.

Now, one can use the -like operator (don't confuse it with the
-contains operator - the naming is sometimes strange):

    Get-Disk | where { $_.Path -like "*#WinDRBD42#*" }

The hash is part of the path component. It is to avoid matching
WinDRBD disks 420, 421, and so on.

Last step is to take the minor number and format it into the
string. This is done via the -f option to string objects
(yes also the syntax is sometimes strange): {0} is substituted
by what follows the -f.

    "*#WinDRBD{0}#*" -f 42

So again the full powershell script to select WinDRBD disks by
minor numbers is:

    Get-Disk | where { $_.Path -like "*#WinDRBD{0}#*" -f 42 }

That's it :) Happy hacking!
