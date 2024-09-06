---
layout: page
title: Installing cygwin under ReactOS
permalink: /reactos-cygwin/
nav_order: 22
parent: Howtos
---

# [](#header-1) Installing cygwin under ReactOS

[Cygwin](https://www.cygwin.org) is a port of the GNU
utilities (like bash, gcc, vi, openssh and many more) to the
Windows platform. It can be used, for example to run
a SSH server on your ReactOS installation. Current
cygwin releases do not run under ReactOS but former
(around 2016) do. Fortunately there is an archive.

To install cygwin on your reactos machine, proceed
as follows:

 * Install firefox from the ReactOS Applications Manager

 * Download the cygwin setup from the [mirror](http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/setup/snapshots/setup-x86-2.874.exe)
   with firefox.
   (this is the 32bit version)

 * Start a cmd shell and navigate to the Download directory.
   Usually this can be reached by entering

        cd "My Documents\Downloads"

 * Start the CygWin installer with the -X parameter (this
   disables signature checking which does not work with the
   archive).

   To do so enter 

        .\setup-x86-2.874.exe -X

   (note that in cmd shell there is tab completion similar
    to bash)

 * Click Next until you reach the mirror sites.

 * Add

    http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/circa/2016/08/30/104223

   as mirror. Be sure there is no extra whitespace after the link.

  * Click next until you can select packages

  * Add the openssh package (if you want to ssh into your machine)

  * Then click Install. Installation will take about 30 minutes

You have successfully installed CygWin under ReactOS. Coming
up next we will show you how to configure the ssh server.

To list all (32-bit) snapshots available go to
[http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/circa/index.html](http://ctm.crouchingtigerhiddenfruitbat.org/pub/cygwin/circa/index.html)

Also useful: run setup exe with -X -O -s download-url

Reference: most ideas were taken from [this site](https://morganwu277.github.io/2017/06/04/Setup-Cygwin-in-Windows-XP-2003/). Since
XP is very similar to ReactOS many XP tips also work under
ReactOS.
