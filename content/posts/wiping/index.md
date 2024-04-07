---
weight: 1
title: "DataWiping = 🩸 Cleaning"
date: 2022-06-04T21:57:40+05:30
lastmod: 2022-06-04T16:45:40+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Data wiping is the new blood cleaning"

tags: ["Forensics", "Anti Forensics"]
categories: ["Forensics"]
---
## Data Wiping is the new Blood Cleaning

Threat actors come in all shapes and sizes, they could be script kiddies trying to run the latest tool for funzies or they could be experienced criminal actors. Either way, they try to hide their activities by wiping data from their computers once the deed is done

One might think using sophisticated tools to **shred** files and even format the hard-drives may be enough to cover their tracks but that's not the case every time.

### Indicators of Compromise
1. AmCache, ShimCache, BAM, DAM
2. SRUM, ShellBags, BagsMRU
3. $UsnJrnl
4. Detecting patterns left by data wiping software
5. Manually analysing and finding inconsistencies in NTFS data transaction logs