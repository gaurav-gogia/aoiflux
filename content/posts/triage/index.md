---
weight: 1
title: "Let's talk about Digital Forensics Triage Analysis"
date: 2022-10-02T21:57:40+05:30
lastmod: 2022-10-02T16:45:40+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Let's talk about Digital Forensics Triage Analysis"

tags: ["Forensics", "Triage"]
categories: ["Forensics"]

resources:
- name: "featured-image"
  src: "triage.jpg"
---

### Spoiler Alert!
We are **not** talking about Emergency Medical Aid or First Aid 😁

### Questions/Answers
{{< admonition question "Question 1" false >}}
What do you normally think of when you hear this term?
{{< /admonition >}}
- File name sub string matches
- File content/metadata **YARA** matches
- File type filter

{{< admonition question "Question 2" false >}}
Or something more advanced?
{{< /admonition >}}
- System generated file/User generated file detection
- Log analysis with pre-defined rules
- Weighted score file ranking with multiple **algorithms** running behind the scenes designed for filtering files

While all these methods are legitimate. They are all relying on the existence of files and file systems. Sometimes, we need to process unknown data streams that may not conform to any known file/file system formats.

### Bulk Analysis
This is where bulk analysis comes into the picture. One of the tools that you can use to perform bulk analysis is bulk_extractor. A battle tested and modern forensic triage analysis software.

Performs all basic regex matches for IPs/URLs/Phone Numbers/Credit Card numbers etc.Attempts decompression of known compression formats to extract content. Understands JSON. Extracts PDF files. Analyses PCAP data streams.

And while this software may feel like peak triage. There is much more room for research for everyone.

### Research Opportunities
{{< figure src="opportunity.jpg" title="Research Opportunity (figure)" >}}

- Embedded triage analysis tools
- Forensic triage in CI/CD pipelines
- Digital forensic triage as a service
- ML for triage
- File system specific useful content detection:
- Slack space usage
- Alternate data stream use
- MBR/GPT mutation/poisoning
- Prefetch files to event log correlation
- Thumbnail preview matching for contraband detection

Sky is the limit. Context aware forensic triage analysis can do wonders in this field. I'll attach my previous post in the comments section to get you some relevant reading material.
