---
weight: 1
title: "NTFS is a Transactional File System"
date: 2022-08-04T21:57:40+08:00
lastmod: 2022-08-04T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "NTFS is a Transactional File System"

tags: ["Forensics", "File Systems", "NTFS"]
categories: ["Shorts"]

resources:
- name: "featured-image"
  src: "ntfs_rely.jpg"

toc:
    enable: false
---

What does it mean? It means every I/O operation is a transaction. And these transactions are performed atomically. Either they succeed or they fail. There are no half/partial writes (in theory).

Do you feel **ntfs** is reliable? Or do you prefer something more proven in the #raid world such as **zfs**?

Higher reliability of filesystems means higher probability of recovering deleted files during an investigation.


{{< admonition tip "Bonus" false >}}
This doesn't mean you can't find ways to subvert it.
👀 ➡️➡️➡️ alternate data streams
{{< /admonition >}}
