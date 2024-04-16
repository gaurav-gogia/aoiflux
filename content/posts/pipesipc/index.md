---
weight: 1
title: "Pipes are Fun!"
date: 2024-04-11T13:23:00+05:30
lastmod: 2024-04-11T13:23:00+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Pipes and Named Pipes for Inter Process Communication"

tags: ["Pipes", "IPC", "LPC", "RPC", "Named Pipes"]
categories: ["Communication"]
---

Pipes are fun. Pipes are cool. All the cool kids use pipes for communication.

You are probably thinking - wait a minute! I thought signals were used for IPC and for triggering specific actions in processes. And you'd be right.
Signals are used to communicate b/w 2 processes. Signals like `SIGINT`, `SIGTERM`, `SIGKILL` can be used to communicate and alter state of processes. However, Pipes can be also be used for IPC.

Note:
ALPC are used for high performance, high throughput IPC in Windows. Checkout how ALPC and CLFS play together. CLFS is highly optimised for performance.

### You have probably already used `pipes` before
Where have I used them? - you ask. Well, let's take a look at this program. Here's a simple example:
```c
#include <stdio.h>

int main() {
	printf("hi");
}
```
Above mentioned program writes output to `stdout`

Same program can be re-written as:
```c
int main() {
	fprintf(stdout, "hi");
}
```

And you can have separate pipes for `stdout` and `stderr` and you can just decide to redirect any of them to some file.

- Use `>` to redirect `stdout`
- Use `2>` to redirect `stderr`

Also, you can redirect these `pipes` to some other process, effectively connecting both the processes to each other.

```sh
./proc1 | grep hula
```
or
```sh
ps aux | grep edge | wc
```

But those `pipes` are ephemeral. They go away as soon as process is done. What to do if you want to have them stay persistent for some time?
You give them names. That way, pipes will live in the files system.

```sh
mkfifo somepipe
```

While it looks as a file, it is not a regular file. FS knows that it is a pipe. This pipe can be used for IPC. When a process writes to it, it blocks the process that wrote to it. And when some other process reads from it, writing process can finally proceed ahead.

## Just use a regular file bro
Files are slow. If you just want to do IPC then using pipes would be faster. This is because files are going to commit data to the disk. Pipes are not going to do that. Pipes will hold on to data only until either
- Writing process lives
- Reading process consumes it
- Whichever happens first from above two

Named pipes are also faster than sockets. And, yes, sockets can also be used for IPC.

## Windows has Pipes too
Some properties of pipes in Windows
- Used for IPC
- Can be one-way or duplex for comm  b/w pipe server and one or more clients
- Have their own file system called NPFS
- Any process can access them depending upon security clearance
- All instances of a named pipe share the same pipe name but each instance has it's own buffers and handles
### Named pipes can be accessed remotely
- IPC can be local or remote
- IPC can be done over RPC
- Useful in distributed computing envs
- IONinja tool can sniff pipes

### Properties of Windows named pipes
- Number of min and max connections
- ACLs -  DACLs and SACLs
- Enumerating and scanning for named pipes
```powershell
# Get the list of named pipes using .NET classes
$namedPipes = [System.IO.Directory]::GetFiles("\\.\\pipe\\") | ForEach-Object { $_ -replace '\\\\.\\pipe\\', '' }

# Output the list of named pipes
$namedPipes
```

### What other methods for IPC in Windows?
1. Shared memory/mapping
2. RPC
3. LPC/ALPC
4. COM - Uses RPC or LPC
5. Windows Messages
6. Sockets
7. Named Pipes
8. Named - Mutex, Semaphore, Events
9. Debugging APIs
10. Regular ol' Files
11. Custom Kernel Driver

# References
1. https://www.youtube.com/watch?v=dhFkwGRSVGk&ab_channel=JacobSorber
2. https://www.youtube.com/watch?v=AHPBaSqI4TI&t=1131s&ab_channel=AllHackingCons
3. https://www.youtube.com/watch?v=NxI5DCM_BfQ&ab_channel=TheSourceLens
4. https://learn.microsoft.com/en-us/windows/win32/ipc/pipes
5. https://learn.microsoft.com/en-us/windows/win32/ipc/named-pipes
6. https://learn.microsoft.com/en-us/windows/win32/ipc/using-pipes