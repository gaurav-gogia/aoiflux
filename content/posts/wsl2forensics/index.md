---
weight: 1
title: "ampering digital evidence is hard"
date: 2023-11-7T21:57:40+08:00
lastmod: 2023-11-7T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "ampering digital evidence is hard"

tags: ["Forensics", "WSL2", "Virtualisation", "HyperV"]
categories: ["Forensics"]

resources:
- name: "featured-image"
  src: "wsl2.jpg"
---

# Overview

This paper discusses detection, acquisition, and post-mortem analysis of WSL2
instances. The paper explores how WSL2 forensics integrates into existing
forensic investigation processes and which tools can be used to extract &
analyse WSL2 images.

## Background

Windows ships Linux kernel along with Windows NT kernel. Windows uses HyperV to
virtualize WSL2 instances. This allows running multiple instances of multiple
Linux distributions using WSL2. MS has created their own Linux kernel by forking
original one from kernel.org. WSL2 uses Plan9 server that starts with the init
process. This Plan9 server is used to share files between Windows and WSL2
instances.

## Hypothesis:

1. WSL2 disk images are VHDX image files.
2. WSL2 disk images can be re-virtualized using common VM management software.

The researchers first identify artefact categories that provide information
about current WSL installations. For this, they have developed a plugin for
RegRipper in addition to a manual guideline for WSL2 artefact detection.

## Testbed

VM with WSL and VM without WSL, both were compared against each other during the
investigation to understand artefacts and changes made by WSL.

1. Windows 10 VM with WSL.
2. Windows 10 VM without WSL.
3. Windows 10 that initially had empty WSL instance used to re-virtualise the
   extracted WSL image.
4. Windows 7 VM – for performing the investigation of the acquired images.

## Results

1. RegRipper plugin helps in identifying whether a WSL2 instance is installed on
   a windows machine or not. Following plugins have been most useful: AppPaths,
   Clsid, Msis, Shimcache, appcompatcache_tln, services, svc_plus
2. Traces of WSL can also be detected in event logs.
3. Detecting presence of WSL instances in registry can sometimes lead to false
   positives.
4. Jumplists did not contain any artefacts pointing at WSL installations.
5. Prefetch files indicate that certain WSL instances were installed and run.
   However, prefetch files can only help identification of those instances that
   were run at least once.
6. Extraction of WSL2 disk image and re-virtualization is trivial.

## WSLDetect Plugin

Helps in finding out which WSL2 distributions are installed and summarise all
relevant information. Tool finds following info from registry.

1. Kernel Version (LXSS)
2. Kernel installer information
3. Application paths (App Paths)
4. WSL2 software packages

## References

[WSL2 Forensics: Detection, Analysis & Revirtualization](https://dl.acm.org/doi/fullHtml/10.1145/3538969.3544439)
