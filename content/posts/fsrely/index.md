---
weight: 1
title: "File System Reliability"
date: 2024-02-07T13:23:00+05:30
lastmod: 2024-02-07T13:23:00+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Realiability of file systems"

tags: ["Forensics", "File Systems", "NTFS"]
categories: ["File Systems"]

resources:
- name: "featured-image"
  src: "rely.jpg"
---

Let's talk about the reliability of file systems from a #forensic investigator's perspective. Our candidate will be #ntfs. Because **Windows**, even with all its quirks, still has huge market.

So, to identify how reliable this file system is. We need to answer following questions:


### Questions
Q1. If a "secure" file deleting tool is used, then what are the chances of recovering the file in question?

Q2. What file system features will help us in recovering the file?

### Answer
```
$LogFile
```

### Summary
In most of the cases, at least this file will remain untouched. It'll be a goldmine of data that will not only help in reconstruction of the file but will also help in establishing a #timeline of events that took place while the file in question was being shredded.