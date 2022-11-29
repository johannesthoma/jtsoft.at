---
layout: page
title: Running Visual C# Compiler under Linux in a Docker container
permalink: /csharp-wine-docker/
nav_order: 24
parent: Howtos
---

# [](#header-1) Building C# programs under Linux

This howto contains several recipes (like getting Mono
running under wine) that ultimatively makes building
C# programs in a docker container under Linux possible.

We use this to compile a C# tool using the Microsoft Visual
Studio C# compiler under Linux (with wine and Mono).

We will explain first how to get wine running in a docker
container based on Ubuntu focal (20.4 LTS). We will install
wine using apt in the docker container. Building the docker
container is driven by a so-called Dockerfile.

Here is the Dockerfile:

    FROM ubuntu:focal

    # prevent tzdata from stalling the installation
    ENV DEBIAN_FRONTEND=noninteractive
    # wine is very noisy by default: this silences most diagnostic messages:
    ENV WINEDEBUG=-all
    # And we want it in that directory in the container
    ENV WINEPREFIX=/wine

    # First some useful tools not included by default:
    RUN apt-get update
    RUN apt-get install --no-install-recommends -y ca-certificates wget apt-utils make git iputils-ping python3 rsync vim xz-utils

    # Wine needs i386: in our case we also want to run inno-setup which is
    # 32 bit.
    RUN dpkg --add-architecture i386
    # See https://wiki.winehq.org/Ubuntu .. we want newer wine versions
    RUN mkdir -pm755 /etc/apt/keyrings
    RUN wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
    RUN wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/bionic/winehq-bionic.sources

    # then from this winehq PPA install the Wine Stable:
    RUN apt-get update
    RUN apt-get install --no-install-recommends -y winehq-stable

    # build default wine dir
    RUN wineboot -i

Place it into a separate directory
To execute it run

    docker build --pull=true --no-cache=true -t my-container docker-root

Sometimes a GUI is not available, so this is how to get Mono
(an Open Source .NET replacement) working on wine in a docker
container from the command line. We use this to compile a C#
tool using the Microsoft Visual Studio C# compiler under
Linux (with wine and Mono).

First, download the Wine Mono tarball from [this Location](https://dl.winehq.org/wine/wine-mono/7.4.0/wine-mono-7.4.0-x86.tar.xz).
