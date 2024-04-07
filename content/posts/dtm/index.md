---
weight: 1
title: "Investigator knows that you've been shredding"
date: 2022-10-25T21:57:40+05:30
lastmod: 2022-10-25T16:45:40+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Investigator knows that you've been shredding"

tags: ["Forensics", "Anti Forensics", "File Systems", "Digital Tool Markers"]
categories: ["Forensics"]
---

## Overview
In one of the previous posts we talked about how [File Shredders Aren't Perfect](https://aoiflux.xyz/posts/shredder). How they leave behind some signs that can be picked during file system forensic analysis. In this article we'll be talkining about all those things

{{< admonition note "Note" false >}}
Our analysis will be limited to just FAT32 & NTFS file systems
{{< /admonition >}}

## File System Forensics
The art and science of understanding how file systems work to unearth artefacts left by interacting with the underlying storage. Modern operating systems and file systems keep a lot of information whenever you save a file. Some of this includes:

1. Full file name (long name & short name)
2. File content
3. Creation time
4. Access time
5. Modified time
6. File content modification history

File system forensics or forensics in general just boils down to a deeper understanding of a lot of fundamentals. All of them cannot be captured in one article.

{{< admonition tip "Tip" false >}}
A file system is **not** a software. It's just a data structure or a blueprint. A way to organize files on your machine.
{{< /admonition >}}

### Where is all this metadata saved?
Most of the information can be found in following files but Windows Event Logs can also help in supplementing your investigation.

1. $LogFile
2. $MFT
3. $UsnJrnl

These files can help in detecting signatures aka digital tool markers left behind by file shredding tools.

### What are these strange files?
These files are created by the file system to keep a record of all of your files/folders in the storage device. These files are specific to FAT32/EXFAT/NTFS file systems. Other file systems work differently. Understanding each and every file system and the way they work is important to investigate file systems.

## Detecting Digital Tool Markers
Before we being our detection, we have to create a testbed for our experiment. Our only requirement here is a virtual machine. And we can follow below flowchart to understand test bed creation.

### Creating test scenario
{{< mermaid >}}
graph LR;
VM(Virtual Machine) --> VHD(Create a new VHD) --> AVHD(Attach VHD to Virtual Machine) --> FVHD(Format VHD to FAT32 or NTFS)
{{< /mermaid >}}

With the test bed created, we are ready to perform our experiment. Following steps can help us understand how to do so.
{{< mermaid >}}
graph LR;
IT(Install File Shredding Tool) --> CF(Create Files in VHD) --> RT(Run File Shredding Tool)
{{< /mermaid >}}

### Performing investigation
For this task we'll be utilising a super cool, free, and open-source digital forensics software called [Autopsy](https://github.com/sleuthkit/autopsy). It is an industry standard software which is used by all major organisations ranging from Big 4 auditing firms to Law Enforcement Agencies.

To perform investigation, all you need to do is ingest your disk image into the software and let the ingest modules run. They can easily parse the file systems and find relevant file system metadat files for you.

## Sample Experiment
This section will explain some sample file shredding tools and their digital tool markers. Please perform the same experiments so you can validate the resulsts.

### CCleaner v5.51.6939
A popular file shredding tool with over 5 billion downloads. 36 million GB of data cleaned every month according to their reports. While the file is recoverable after normal delete option. It also provides secure delete option with 3, 7, and 35 passes. Let's discuss DTMs for each file system below.

**FAT32**
1. Short & Long filenames changed to Z up to original file name length
2. File access time is changed
3. Entire clusters containing the file content are cleaned, including slack space
4. Originally allocated disk space is filled with 0x00

**NTFS**
1. Short & Long filenames changed to Z up to original file name length
2. $MFT does not retain any original file name info
3. Created & Accessed timestamps for the wiped file in $MFT remains unchanged
4. Modified & Changed timestamps reflect the time of wiping
5. INDX information is no longer available
6. Does not wipe $LogFile
7. $LogFile tells us all the names a file has had over a span of time
8. Files that generate temp content upon interaction such as office files retain those temp traces on the disk, thus help in file recovery. If those files or the folders containing those files are not selected.

### FileShredder v2.5
This tool offers a basic file/folder secure deletion option with no advanced features like CCleaner. Let's discuss DTMs for each file system below.

**FAT32**
1. File name is changed to a random number
2. Original file container maintains original name and created date
3. Modified time stamp is changed due to the copy and content change
4. File contents are changed to Z
5. After wipe, allocated file size is always 0

**NTFS**
1. Original file container and data is deleted
2. File is renamed to random numbers
3. All created time stamps are left original
4. Modified timestamps are post wipe period
5. Does not affect $LogFile

### Eraser v6.2.0.2982
Eraser offers 13 wiping algorithms ranging from one pass to 35 including well known algorithms listed in US DoD 5220.22M. By default, it is configured to use Guttman's 35 pass algorithm. Let's discuss DTMs for each file system below.

**FAT32**
1. Post deletion files are given a random alphanumeric name without extension
2. All available timestamps are changed to 01-01-1980
3. File size is reported to be zero

**NTFS**
1. Post deletion files are given a random alphanumeric name without extension
2. All available timestamps are changed to 01-01-1601 except for the Date Changed value in $MFT
3. Date Changed value in $MFT reports time of wipe
4. Does not affect $LogFile

### Freeraser v1.0.0.23
This software offers three modes: fast, forced, & ultimate. By default, “fast” mode is selected. The software does not elaborate on the algorithm used. Let's discuss DTMs for each file system below.

**FAT32**
1. No change in file name or file size
2. Modified and accessed date/time reflects time of wipe
3. Created date/time remains unchanged
4. File size metadata is available

**NTFS**
1. No change in file name or file size
2. Modified and accessed date/time reflects time of wipe
3. Created date/time remains unchanged
4. File size metadata is available
5. Does not affect $LogFile

### Wipefile v2.4.1.0
This software offers 14 different algorithms for the users to select. But by default, a single pass algorithm is used. Let's discuss DTMs for each file system below.

**FAT32**
1. Created time and modified timestamps are replaced with 01/01/1980
2. File names replaced with random alphanumeric string including extension
3. File name changes are done up to original file name

**NTFS**
1. Created time and modified timestamps are replaced with 01/01/1980
2. File names replaced with random alphanumeric string including extension
3. File name changes are done up to original file name
4. Does not affect $LogFile

### DPwipe v1.1
This software offers 6 different wiping algorithms. Default option is US DoD 5220.22-M 3x. Let's discuss DTMs for each file system below.

**FAT32**
1. Three file containers are created
2. All containers have identical metadata
3. One container has original file name, one has file name changed to all ‘a’ characters
4. FAT short name is changed to a random string
5. Created timestamp remains intact
6. Modified timestamp reflects time of wipe

**NTFS**
1. Updates file name to a single ‘a’ character
2. File modified, accessed, and created times are set to 01/01/1980
3. $MFT timestamps retains original file creation time
4. $MFT timestamps reflects time of wipe for changed time
5. Does not affect $LogFile

### Moo0 Shredder v1.21
This software offers 5 types of wipe options. By default, Shred Once (Normally Unrecoverable) algorithm is selected. Tool doesn’t explain how different algorithms work. Informal names are assigned to each algorithm such as:
- Vaporize (vanish it!)
- Into Ashes (Extremely Secure)
- Extra Careful (Even More Secure)
- Shred Once (Normally Unrecoverable)

Let's discuss DTMs for each file system below.

**FAT32**
1. File names are replaced with random string same length as original file name
2. Randomly generated modified, accessed, and created date and times are assigned

**NTFS**
1. File names are replaced with random string same length as original file name
2. Randomly generated modified, accessed, and created date and times are assigned
3. $MFT timestamps retains original file creation time
4. $MFT timestamps reflects time of wipe for changed time
5. Does not affect $LogFile

### Bitkiller v2.0
This software offers five wiping algorithms. By default, it is set to wipe with ‘Random data’. Let's discuss DTMs for each file system below.

**FAT32**
1. Creates two file containers
2. First one has original file name and timestamps
3. Second container has a 9 character long random string name
4. Changed time reflects time post wiping

**NTFS**
1. All the original file information is changed
2. Changed time reflects time post wiping
3. $MFT timestamps remain unchanged
4. Does not affect $LogFile

## Conclusion
All of these software work similarly. They seem to be changing file content and file name or both before writing it back and deleting it. They don't really affect $LogFile, making them recoverable. A lot of digital tool markers here seem to be common among differnt software, this may cause false positives.

### What about $LogFile?
While it does look unbeatable. It is not. It doesn't matter how many logging mechanisms a file system employs. At the end of the day, they have limited storage space. It is only a matter of time(passes) before they can be overwritten.

### Cool story but how do I create my own?
It is simpler than you think it is. Start simple and then move forward with more complex features. Rewrite existing file content, file name, modified/creation time stamps. So many features to add. The more you study investigative techniques, the better you can understand ways to bypass them.

I've made one. It is a part of some forensics/security tools that I created to understand these topics better. I call my tool [dfis-utils](https://github.com/gaurav-gogia/dfis-utils). It's written in go. My tool is comparable to CCleaner v5. It's features include:

1. Multi-threaded
2. Secure delete with 1 - N pass options
3. Changes file name with a random string upto length of original file name

## References
1. [Digital tool marks (DTMs): a forensic analysis of file wiping software](https://www.researchgate.net/publication/334764352_Digital_tool_marks_DTMs_a_forensic_analysis_of_file_wiping_software)
2. [Understanding Anti-forensic Techniques with Timestamp Manipulation (Invited Paper)](https://ieeexplore.ieee.org/abstract/document/7785797/)
3. [Forensic analysis of anti-forensic file-wiping tools on Windows](https://onlinelibrary.wiley.com/doi/abs/10.1111/1556-4029.14907)
4. [NTFS Overview](https://learn.microsoft.com/en-us/windows-server/storage/file-server/ntfs-overview)