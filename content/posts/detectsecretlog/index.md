---
weight: 1
title: "Detecting & Redacting Secrets in Logs"
date: 2022-11-27T21:57:40+08:00
lastmod: 2022-11-27T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Detecting & Redacting Secrets in Logs"

tags: ["Forensics", "Security", "Logging", "Privacy"]
categories: ["Privacy"]
---


Logging is important for all sorts of purposes. Windows event logs help in detecting intrusions. EDR/IDS/IPS/Firewall logs help in catching malware. But these logs can contain sensitive information. What if you end up typing your password in the username field? Rookie mistake. However, it can cost a lot. Windows Event 4625 is going to report it. Almost every other application that logs failed login attempts is going to log this bit of info. NIST has a very comprehensive guide on generating and maintaining logs.

## How do we deal with it?
There is no silver bullet solution here. There will always be edge cases that may lead to false positives or worse problems. This is why we run threat modeling exercises, but this is a topic for another time.

Few things we can attempt:
1. Secrets Detection
2. Homomorphic Encryption
3. Shifting Left
4. Awareness Campaigns

### Secrets Detection
One of the most obvious solutions is to detect the secrets and redact them. However, detecting secrets is a complex task that can lead to numerous false positives. This detection can be done based on a word list or entropy detection. While we can try to flag all _possible_ secrets, there is never a way to be sure of what's private information and what's not.

### Homomorphic Encryption
An experimental solution could be to use an encryption scheme that allows search engines to search encrypted logs [2]. Real-world applications of homomorphic encryption are limited, making it a prime candidate for further research in many verticles, including privacy-preserving logging.

### Shifting Left
Another possible solution would be to nip the evil in the bud by detecting sources of logs. Log sensors or parts of log sensor devices and applications can be configured to report if they are possibly reporting any private data with different levels of privacy and confidence. Baselines and thresholds can be set up to customise just how strictly privacy needs to preserved for certain environments.

### Awareness Campaigns
When all else fails, train the user to just not write the password in that username field 🤷‍♂️🤷‍♀️🤷. Sometimes we have to protect ourselves from unintentional self-sabotage.

## Exploiting Defense Mechanisms
While we are figuring out how to accurately detect and redact secrets, adversaries will be busy figuring out how they can abuse secret detectors to hide their payloads, secret messages, backdoors, or malware code. There will always be a trade-off between security & privacy.

## References
1. [NIST - Guide to Computer Security Log Management](https://csrc.nist.rip/library/NIST%20SP%20800-092%20Guide%20to%20Computer%20Security%20Log%20Management,%202006-09.pdf)
2. [Homomorphic Encryption](https://www.sciencedirect.com/topics/computer-science/homomorphic-encryption)
