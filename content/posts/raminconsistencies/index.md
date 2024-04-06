---
weight: 1
title: "Inconsistencies in Memory Forensics"
date: 2024-04-08T21:57:40+08:00
lastmod: 2024-04-08T16:45:40+08:00
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "An Experimental Assessment of Inconsistencies in Memory Forensics | ACM Transactions on Privacy and Security"

tags: ["Memory Forensics", "Forensics", "Vector Clocks"]
categories: ["Forensics"]
---

Based on 360 memory dumps of running Linux systems. A number of inconsistencies were observed. Almost 1/3 of all the memory dumps had an empty process list - meaning those dumps were incomplete. Results are based on a new way of determining `causal inconsistency` in memory dumps. While the factors are unclear, but in general they correlate with the level of concurrency.

## Obtaining memory dump
Two main approaches for this - **clean** and **dirty** approaches.
### Clean
1. The system can be halted and memory can be accessed by independent means.
2. Usually works on virtual machines.
3. Can also work if memory hardware is modified to provide such functionality.
4. When system is in halted/paused state - all contents can be easily read from memory and written to a file.
5. Another method is to use cold boot attacks.
### Dirty
1. One of the established methods is to use `post-hoc` kernel level dumping. The approach is self referential because dumping software ends up accessing it's own memory.
2. This approach is dirty because system and user processes remain active while memory dump is being taken.
3. Some well known problems that can be observed due to this method include page smearing because of the contents of the acquired pages continue to change.

### What causes Page Smear?
Imagine a computer system where you have memory addresses ranging from `0000 0000`  to `FFFF FFFF` - all the user mode programs take space in the lower address space starting from `0000 0000` while all the higher addresses are acquired by kernel code starting from `FFFF FFFF`.

As you start acquiring evidence starting from `0000 0000` moving towards `FFFF FFFF` there will be chances where you may end up capturing let's say `01FFF 0000` part of memory at a point in time when there was no malicious activity was using that address range. But as you move forward, something malicious happens there.

Due to the temporal nature of memory forensics, it is difficult to acquire memory with high fidelity and 100% forensic soundness. There will always be chances of missing out on important artefacts. This probability of missing crucial data increases with increasing concurrency/number of processes running on the machine.

## Hypercalls
Vömel and Freiling defined a practical criteria for evaluating memory forensic acquisition tools. Their method was to insert `hypercalls` in into the code of the tool they were evaluating.

- Hypervisors provide a calling mechanism for guests - they are called `hypercalls`. These `hypercalls` are analogous to `syscalls`.
- They serve as a mode of communication b/w guest OS and hypervisor.
- Every `hypercall` defines a set of input and/or output params.
- Params are defined in terms of memory based data structures.
- All elements of the input and output data structures are padded to natural boundaries up to 8 bytes (that is, two-byte elements must be on two-byte boundaries and so on).
- In most cases a `hypercall` action is atomic.
- Each hypercall corresponds to a specific operation provided by the hypervisor.
- Guest OS makes `hypercall` to request resources or do IO stuff from hypervisor.
- There are multiple calling conventions for calling `hypercalls`
- Can be defined in two categories: `simple` and `rep`
	- `simple` hypercalls perform a single atomic action
	- `rep` hypercalls perform a series of `simple` hypercalls
	- `rep` hypercalls involve a list of fixed-size input and/or output elements, and the hypervisor processes them in list order.
- In KVM, hypercalls use the **HYPCALL instruction** with code 0 and the hypercall number in register `$2` (`v0`)
- Up to four arguments can be placed in registers `$4-$7 (a0-a3)`, and the return value is placed in `$2 (v0)`
### Hypercall Trivia
- A `domain` is an `isolated execution environment`.
- Each `domain` represents a separate `virtual machine (VM)` or a `partitioned portion` of the `physical hardware`.
- Domains are created and managed by the hypervisor, which ensures their isolation from one another.

## Contributions of this Paper
1. A new way to estimate `causal consistency` of a memory dump by injecting `pivot` processes that generate predictable patterns from which `causal` relations can be inferred.
2. A hierarchical classification of memory dumps based on the ability to analyse `causal consistency` using `pivot` processes.
3. Empirical assessment of the practical consequences of observable inconsistencies based on a sample size of 360 dumps.
	1. 102 / 360 had empty process list.
	2. Out of remaining 258 valid dumps - 180 were selected based on some params.
	3. 80 / 180 contained VMA inconsistencies while 73 / 180 presented causal inconsistencies.
4. Factors influencing inconsistencies are not very clear but in general, higher concurrency leads to higher level of inconsistencies
	1. Main factor influencing VMA inconsistency seems to be system load.
	2. Causal inconsistencies seem to correlate with the number of threads running.

## Kernel Level Memory Dumping
Most memory acquisition tools that operate at Kernel/OS level work in a linear fashion. They read memory from start to end in a single pass.

### Virtual addresses
- Virtual addresses help in isolating every process from each other.
- Virtual addresses allow processes allocate way more memory than physically available.
- A process can only access its own virtual address space.
- Virtual addresses are split and stored in fixed sized blocks called `pages`
- Virtual address `pages` are contiguous but their counterpart physical memory `frames` do not have to be contiguous.
### A primer on virtual address translation
- When a `page` is accessed, MMU is used to identify the corresponding `frame`
- On `x86-x64` four levels of paging data structures used to translate `pages` into `frames` when `page` size is `4 KiB`
- Entry point is page map level 4 `PML4`
- `PML4` contains references to page directory pointer tables `PDPT`
- `PDPT` contains references to page tables `PT`
- Every entry in `PT` refers to a `frame`
- Each step in this process contains extra information like access rights.
- Physical address of a process' `PML4` is loaded into CR3 control register.
### Analogy
Imagine you are in a library `PDPT` is the main catalogue of the library that contains pointers/references to `PD`, which lead to specific subjects within the sections where we find `PT`, which in turn contain references to the specific books where we find `frames` that contain references to the page in the book where actual information is stored.

So when you want to find something in the library you go to the main catalogue `PDPT` that has all the sections, you select specific `PD` to find the subjects in a section where you find `PT` that points you to the books and out of those books you find `frames` to find the right page for your content.
## Defining & Observing Causal Inconsistency
It is defined by how consistent is the state of causally dependent changes on memory. If an event `e1` happens and then memory is copied and then `e2` happens and then more memory is copied then a causal relationship between `e1` and `e2` can be explained.  In contrast if memory is copied before `e1` happens and then more memory is copied after `e2` happens then we won't be able to establish a relationship because we don't have any information about `e1`.
#### Causal relations can be tracked with a vector clock
Each memory region is assigned a vector. Every vector has a capacity of total number of memory regions including itself. Every memory region is indexed in every vector. So all memory regions maintain a state for every region indexed in their respective vectors.

Vector clocks keep track of local order of events and connections to other memory regions. Processes carry the values of the vector of their last accessed region with them which are used when the vector of the next visited region is updated.

A vector clock represents causa past of an event. By comparing vector clocks of different regions it is possible to partially construct causal relationship between events.

## Experimental Setup
Objective of experiments is to study *likeliness* of occurance of inconsistencies under different degrees of concurrent activity in a computer.
### Tool Selection
1. LiME - Kernel level memory acquisition tool. Implemented as a loadable Linux kernel module. Hasn't been maintained very well in recent years though.
2. AVML - User mode memory acquisition tool. Uses one of `/dev/crash` or `/dev/mem` or `/proc/kcore` to acquire memory. Does not work on machines where the `kernel lockdown` mode is enabled.
### Experiment Environment
- Virtual Machine
- RAM = 4GB
- OS = Ubuntu 18.04.2 LTS
- Linux Kernel: 4.15.0-177-generic
- Minimum system installation without GUI
- Default RAM configuration
- Swapping was kept enabled
- CPU Count = 4
### Programs executed
- Script used to control the Pivot program
- Pivot program itself for controlling concurrency
- LiME
- `stress` program that comes with Ubuntu
### Running pivot program
- Initialises a synchronised linked list - initially with just one thread.
- Several threads are created
- Some remove elements, some insert new elements, seemingly at random locations
- Random pauses are added in between while adding/removing elements
- Each element has a vector clock that is updated when a thread removes or inserts a list element
- Between elements - a buffer is added to ensure constant distance b/w list elements
### Test Conditions
1. System load generated by the `stress`
2. Number of active threads in `pivot` program
For varying results `pivot` program was was used with threads 1, 2, and 4 while the `stress` program was run for 120 seconds.

## Caveats

1. Lime skips pages by default while acquiring if they take longer than 1 second to read
2. 4 Core CPU was required because with 2 cores memory images were smaller than expected
3. Classification of datasets
	1. Total: 360
	2. Broken: 0
	3. Meaningless: 102
	4. Valid: 258
	5. Final: 180
4. Broken: Not all memory was acquired
5. Meaningless: Could not be loaded into Volatility - process list is incomplete
6. Valid: Contains pivot and details could be extracted
## Results
1. No system load - no VMA inconsistencies
2. High system load -  high VMA inconsistencies
3. No of threads doesn't have correlation with VMA inconsistencies
4. High system load - higher acquisition time
5. Higher threads have higher impact on causal inconsistencies as compared to system load

## Future Work
1. Is there a point after which increasing system load or threads doesn't affect inconsistencies?
2. Emulating normal user environment better than synthetically generating load for results closer to a more real world scenario.

## References
1. [Hypercall Interface | Microsoft Learn](https://learn.microsoft.com/en-us/virtualization/hyper-v-on-windows/tlfs/hypercall-interface)
2. [volatilityfoundation/volatility: An advanced memory forensics framework (github.com)](https://github.com/volatilityfoundation/volatility)
3. [marcotcr/lime: Lime: Explaining the predictions of any machine learning classifier (github.com)](https://github.com/marcotcr/lime)
4. [An Experimental Assessment of Inconsistencies in Memory Forensics | ACM Transactions on Privacy and Security](https://dl.acm.org/doi/abs/10.1145/3628600)