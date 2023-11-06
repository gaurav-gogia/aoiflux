---
weight: 1
title: "ampering digital evidence is hard"
date: 2023-11-6T21:57:40+08:00
lastmod: 2023-11-6T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "ampering digital evidence is hard"

tags: ["Forensics", "Anti Forensics"]
categories: ["Forensics"]
---

# Overview

Depending upon when does a threat actor gain access to the evidence, their level
of effort required to tamper with the evidence and the probability of tamper
detection can change. For this discussion, let's talk about volatile memory
analysis and evidence tampering in volatile memory images.

## Before we move forward, let's set some expectations

1. Threat actor gets access to memory image before its hash is generated
2. Threat actor understands memory forensics to some degree

Now this threat actor have several options to tamper with the memory image. Here
are some methods that they could use -

1. Create a brand new VM and run some actions to plant fake evidence. Create the
   new memory image. This simulation image should be as close to the original
   memory image.

2. Modify the existing image's data structurers to plant new data like
   timestamps, bash history, ARP cache etc.

3. Modify the existing image by planting random data in free locations of the
   memory image.

However, these methods aren't enough. Experienced investigators will notice
discrepancies in timestamps, process start times, port mapping, etc.

Overall, efforts to tamper the evidence simply are too high. Also, a lot of
times, it is easier to detect tampered volatile memory images.

But what about dead computer forensics? Hard drives are a totally different
ball-game.

## References

[Tampering with Digital Evidence is Hard](https://www.sciencedirect.com/science/article/pii/S2666281720300196)
