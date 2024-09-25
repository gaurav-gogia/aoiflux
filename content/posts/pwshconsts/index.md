---
weight: 1
title: "Quirks of PowerShell"
date: 2024-09-20T21:14:49+05:30
lastmod: 2024-09-20T21:14:49+05:30
draft: false
author: "Gaurav Gogia"
authorLink: "https://linkedin.com/in/gaurav-gogia"
description: "Nuanced behavours like case-insensitive variables and Constant manipulation"

tags: ["PowerShell", "quirks"]
categories: ["quirks"]

toc:
    enable: false
---
You may have heard of nuanced behavour depicted by other programming languages like Python and JavaScript. But have you seen how weird PowerShell can be at times? Let's talk about it in this short article.

## Here's some code for reference:
```powershell
Set-Variable -Name TabCode -Value @{
    T1 = 99
    T2 = 98
} -Option Constant;

function Test-Table() {
    return $TabCode.T1;
}

# this will not work, we cannnot over-write a constant after-all
# $TabCode = "abc"

function Main() {
    Write-Host $TabCode.T1;

    $tabCode = Test-Table -opt "one";

    # Notice how $TabCode loses data - pwsh is case insensitive
    Write-Host $TabCode.T1;

    # you'll find the returned value here.
    Write-Host $TabCode

    # updated $tabCode but $TabCode has been over-written
    $tabCode = "test";
    Write-Host $TabCode


    # and why not over-write the constant itself just for the fun of it
    $TabCode = "abc";
    Write-Host $TabCode
}
Main;
```
{{< admonition tip "Tip" false >}}
Script level variables that are ReadOnly instead of Constant are also affected by this bug.
{{< /admonition >}}

I found this feature/bug while I was writing some functions to play with registry. And it took me over 16 hours to debug this issue because I kept hitting a wall. I had no idea, why my code wasn't working. I almost verbally screamed at the laptop "Why do I keep losing values from this hashtable?". I thought, maybe I was overwriting my variables somewhere and I wasn't paying attention. So, I decided to make it into a constant. Constants are great right? I'll for sure get an error if I try to overwrite them. But PowerShell said NO.

Or rather, it didn't say anything, at all. Ghosted me. Gave me that good ol' cold shoulder treatment. So I decided to spin up the debugger and start debugging line by line and that's where I noticed two problems.

1. Variables are case-insensitive
2. When a constant is accessed in a function - PowerShell will allow you to overwrite it.

It doesn't make any sense. I don't get it. I agree with the first point, but the second one? I don't know. Also, just to clarify, I am not using some super old version of PowerShell. I have tested this behaviour on PowerShell 5.1 and PowerShell 7.4.5

I encourage you to try this code. Maybe you'll find something interesting. I can smell some vulnerabilities hiding somewhere due to this constant overwriting bug. Also, if you write a lot of PowerShell scripts. Pleas DO NOT rely too much on script level constants. Always debug and properly review code. Things can go really bad because PowerShell will allow script level constant overwriting in functions.

## What's the Solution?
It is simpler than you may think. Just explicitly use GLOBAL constants. You have to mention it while creating the constant object itself. Scope needs to be specified. Otherwise, PowerShell will assume and make it a Script level constant instead.

### What's a Script level constant again?
They are limited to the script or function where they are defined. However, due to this but they can be overwritten. See code above.

### Solution Code

```powershell
Set-Variable -Name TabCode -Value @{
    T1 = 99
    T2 = 98
} -Option Constant -Scope Global;

function Test-Table() {
    return $TabCode.T1;
}

# this will not work, we cannnot over-write a constant after-all
# $TabCode = "abc"

function Main() {
    Write-Host $TabCode.T1;

    # This time overwriting will fail and you will get an error message
    $tabCode = Test-Table -opt "one";

    # However, this will still NOT stop PowerShell from overwriting the variable
    Write-Host $TabCode.T1;
}
Main;
```

## What's a more fool-proof solution then?
To ensure that overwriting does NOT happen at all. You need to ensure that a PowerShell script ALWAYS stops whenever it encounters some error instead of moving forward with execution. For that you just need to update an in-built environment variable at the top of your scripts.

This variable is `$ErrorActionPreference;` and by default its value is set to `continue`. This value must be changed to `stop`, and you'll finally have global constants that will never change value in your script, ever.

{{< admonition tip "Tip" false >}}
This technique also works with global ReadOnly variables. Go ahead and try it out ^.^
{{< /admonition >}}


### Fool-Proof Solution Code

```powershell
$ErrorActionPreference = 'Stop';

Set-Variable -Name TabCode -Value @{
    T1 = 99
    T2 = 98
} -Option Constant -Scope Global;

function Test-Table() {
    return $TabCode.T1;
}

# this will not work, we cannnot over-write a constant after-all
# $TabCode = "abc"

function Main() {
    Write-Host $TabCode.T1;

    # This time overwriting will fail and you will get an error message
    # Also, because we ensured that the scripts will stop upon first error
    # No variable overwriting will happen.
    # The script will DIE before it lets anyone overwrite a global constant!
    $tabCode = Test-Table -opt "one";

    Write-Host $TabCode.T1;
}
Main;
```