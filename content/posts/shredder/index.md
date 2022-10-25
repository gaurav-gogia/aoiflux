---
weight: 1
title: "File Shredders Aren't Perfect"
date: 2022-10-16T21:57:40+08:00
lastmod: 2022-10-16T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "File shredders can be traced back due to the DTMs they generate"

tags: ["Forensics", "Anti Forensics", "File Systems", "Digital Tool Markers"]
categories: ["Forensics"]

resources:
- name: "featured-image"
  src: "shredder.jpg"

toc:
    enable: false
---

So, you've used a file shredder tool to securely delete all the files from your #windows machine and formatted the **hdd** just for good measure.

That makes you secure. Right?

Not really. Without getting into the specifics. Modern operating systems and file systems record everything.

File shredder tools leave behind their own signatures. These signatures aka digital tool markers (dtm) can be detected and can even be used to attribute them to some specific file shredding utilities.

What are these digital tool markers? How do we identify them? Please wait for a longer post with more details ^.^

### We'll be talking about:
- File system forensics
- Digital tool marker detections
- Some popular file shredding tools and digital tool markers left behind them
- We'll even discuss some techniques we may be able to use to confuse analysts who know about digital tool markers

Upcoming post will probably include a lil' bit of self-promotion :)