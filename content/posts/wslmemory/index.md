---
weight: 1
title: "ampering digital evidence is hard"
date: 2023-11-5T21:57:40+08:00
lastmod: 2023-11-5T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "ampering digital evidence is hard"

tags: ["Forensics", "WSL", "Memory Forensics"]
categories: ["Forensics"]
---

# Overview

Memory forensics has a special place in my heart. Mostly because research papers
associated with it are usually more from an engineering perspective than an
investigative one.

So, I read this super cool paper about analysing processes running in WSL on a
Windows 10 machine. AVs understand PE32+ executables very well. Windows memory
data-structures are also well understood. However, processes running in WSL are
a different ball game. Same problems plague memory forensics tools.

## A little background

MS had this project called drawbridge. Their objective was lightweight
sandboxing and virtualisation. This project proposed two new process types -
_minimal_ process & _pico_ process.

WSL process is the pico process that hosts all WSL based linux distributions and
runs all userland linux processes inside of it. Effectively allowing users to
run ELF binaries inside Windows machines.

## Anatomy of a WSL process

1. `wsl.exe` is a userland CLI program that users can use to interact with rest
   of the WSL. Users can run multiple instances

2. `LxssManager` A windows service that facilitates communication between
   `wsl.exe` / `bash.exe` processes and the `pico` provider.

3. `lxss` A Windows system service that serves as the WSL pico provider

4. `/init` A Linux pico process that facilitates communication between Windows
   processes and its descendants. `lxss` creates one `/init` process per
   instantiated Linux distribution.

## Recovering Process Name

To find process name `LxpThreadGroupSetExecutable` function was analysed. This
function suggests that WSL pico provider creates a unicode encoded string name
for every process. A pointer to this string is then stored process's PicoContext
at a fixed offset.

## References

[Memory forensics and the Windows Subsystem for Linux](https://www.sciencedirect.com/science/article/pii/S1742287618301944)
