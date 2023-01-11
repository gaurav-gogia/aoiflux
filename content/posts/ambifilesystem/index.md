---
weight: 1
title: "One Partition N File Systems"
date: 2023-01-11T20:24:14+0000
lastmod: 2023-01-11T20:24:14+0000
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "One Partition N File Systems"

tags: ["Forensics", "Anti Forensics", "File Systems"]
categories: ["Forensics"]
---

Let's start by establishing some ground rules. A **file system** is just a **data structure**. It is not software. It only defines how files should be saved. Kernel/User level drivers are written to implement file systems. Just as one would use, say linked lists to keep track of items in the to-do lists.

For the most part, we are used to looking at a 1-1 relationship between partitions and file systems. Most of the file system forensic software seem to make a similar assumption. However, it is possible to have multiple file systems in a single partition.

How do we do it? Let us see an example: For this example, let us take a look at NTFS & FAT32 file systems due to their flexible nature.

## How?
The main idea is to use one file system as the _host_ while the other one as the _guest_ file system. Additionally, we used the options to mark specific blocks as "allocated" or "damaged" to protect areas in the partition that could potentially cause conflicts if they were written.

In this scenario, NTFS is the host file system, while FAT32 is the guest file system.

1. Boot sectors of both NTFS & FAT32 are compared manually using Hex Editor.
2. Important data bytes are identified.
3. Boot sector of FAT32 is super-imposed on the boot sector of NTFS.

Following images will show what this combined boot sector looks like:

{{< figure src="ntfs_fat32.png" >}}
{{< figure src="ntfs_fat32_bytes.png"  >}}

## Why?
To confuse forensic examiners and digital forensics software. While a computer may interpret these file systems normally, it will be difficult for the file systems to parse the file systems since they will not understand which parser should be run. Assuming one file system per partition will make things difficult.

{{< figure src="conf.png" >}}

For more information on this topic, please read the linked research paper below ^.^

## References
1. https://www.sciencedirect.com/science/article/pii/S2666281722000804
2. https://learn.microsoft.com/en-us/windows-server/storage/file-server/ntfs-overview
3. https://learn.microsoft.com/en-us/troubleshoot/windows-client/backup-and-storage/fat-hpfs-and-ntfs-file-systems
