---
weight: 1
title: "What the diff?!"
date: 2024-05-29T00:00:00+05:30
lastmod: 2024-05-29T00:00:00+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Basics of Patch Diffing"

tags: ["Patches", "Reverse Engineering", "Patch Diffing"]
categories: ["Patch Diffing"]

resources:
- name: "featured-image"
  src: "pdiff.png"
---

## Overview
_If it works, don't touch it!_

How many times have we heard this phrase? How many memes have we shared around it? And yet, change is inevitable. There will always be need of code refactor. There will always be bugs in software whether they are related to security or some function.

If there's no bug, then the design will go obsolte at some point in time.

But the story doesn't end here. Here are some questions that you may find yourself asking depending upon your role:
1. What design changes were made between two releases?
2. Which function was refactored since last time that increase search speed?
3. Which API was patched to ensuring sanitisation of all the inputs?

These are just some examples against a set of questions that **you** can ask everytime you come across a new software update.

But we are getting ahead of ourselves, let's talk about some basic terms before we move forward into the topic.

### Key terms
1. **Patch:** A bug-fix or a software update indicating changes in a file or a set of files.
2. **Diff:** Difference between two files
3. **Hunk:** Contiguous block of changes in a single file, between two files.

Finding out the differences between current release and the previous release is the process that defines **patch diffing**. Common use-cases include figuring out exact changes made between two versions for version control and helping security researchers understand what changes were made to which function, in order to patch the vulnerability.

{{< figure src="diff.png" >}}

**YOU** have probably been doing patch diffing all this time and you may not have even realised it. Remember that time when you were looking at a pull request from your friend on your favourite version control site for code manaagement? That process was pretty close to patch diffing.

{{< figure src="diffalready.png" >}}

## Why diff?
1. Find differences between patched and unpatched software.
2. Identify more potential points of weaknesses.
3. Use diff to deploy atomic units of code as patches instead of entire files while managing patches.

### Tools of the trade
1. diff
2. meld
3. git diff
4. bin diff
5. darun grimm
6. diaphora

### More motivation for diff
As a security engineer you may have to reverse engineer different malware strains and re-packaged malware at times. These are the moments where you can make use of patch diffing or in this case malware diffing to find differences and similarities between multiple strains of the same malware.

The same method can also be potentially used to attribute malware to a specific threat actor simply by developing a signature of their code writing style.

Some popular product with critical vulnerabilities may release quick hot-fix as a patch. However, that hot-fix may not cover all potential attack vectors in that patch. Diffing at the right time can lead to discovery of more attack paths. Through this, you can report those new attack vectors to the company through their vulnerability disclosure program, thus helping them secure their product & customers; all while you gain some good reputation within the community.

## How do I diff?
I'll discuss diffing from a reverse-engineer's perspective. First step is to pick any target of your choice. It could be a DLL file or a driver or some shared compiled object or some PE32+ or ELF or something else. After that you need to find the reverse engineering tool of your choice. Some of the free ones are:

1. IDA Freeware
2. Binary Ninja
3. Radare2 & Cutter
4. Ghidra
5. JADX
6. Procyon
7. DotPeek
8. DnSpy

Now, depending upon your target, you may need to find some symbol files or debug symbols or something along those lines. Windows executables usually  have some PDB associated with them. Windows related symbol files can be dowlnoaded using Microsoft website or you can use some open-source tool that can help you with it.

Analyse the files in your favourite tool, ensure symbols are also used to get as much data about the executable as possible.

Export the code in a format that can be utilised by some kind of diffing tool. Example: Ghidra has an extension that allows you to export out BinDiff comptabile files with all the details stored in this new exported file.

As a final step, all you need to do is load both the exported files into BinDiff and run the diff. Of course, all this needs to be done after setting up BinDiff, project, workspace, and completing the basic configuration. Tools like BinDiff will show you differencess in exact locations. Which function was modified and how.

## Example/Case Study
Case study of an important vulnerability:  **CVE-2023-24880** -> Windows SmartScreen Security Feature Bypass Vulnerability. Allows threat actor to run any arbitrary executable on target machine bypassing Windows Defender & SmartScreen.

This vulnerability was actively expl byoited by Magniber Ransomware last year. The vulnerability was spread through modified `msi` installers as payload.

{{< figure src="diffhow.png" >}}

### Why does this vulnerability work?
The `DoSafeOpenPromptForShellExec` function within Windows SmartScreen initializes the `edi` register to `0`, designating the default behavior to permit file execution. Subsequently, this function invokes another routine to verify the Authenticode signature of the executable file. If an error occurs during this verification process, the `edi` register remains unchanged. If `edi` retains the initial value of `0`, the executable file is allowed to execute by default.

{{< figure src="simplified.png" >}}

## References
1. [NVD](https://nvd.nist.gov/vuln/detail/CVE-2023-24880)
2. [MSRC](https://msrc.microsoft.com/update-guide/en-US/advisory/CVE-2023-24880)
3. [Googel TAG](https://blog.google/threat-analysis-group/magniber-ransomware-actors-used-a-variant-of-microsoft-smartscreen-bypass/)