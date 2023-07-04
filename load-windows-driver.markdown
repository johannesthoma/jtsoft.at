---
layout: page
title: Quick and dirty loading a windows driver
permalink: /load-windows-driver/
nav_order: 3
parent: Howtos
---

# [](#header-1)How to quickly load a windows driver

If you just want to check if a certain sys file loads
into the Windows/ReactOS kernel, you can use the sc
utility to create a basic service and to load the driver.

This assumes that you have the driver as a sys file
(like windrbd.sys). Note that sys files are really
exe file but with the native flag set (meaning that
they are a kernel driver).

To create the service, use

    sc create mydriver type= kernel binpath= c:\path\to\mydriver.sys

(Note the strange spacing around the equal sign)

Then you can start the driver as usual with

    sc start mydriver

That's it. No INF or CAT files necessary and sc exists on
virtually all Windows platforms (including ReactOS).

