---
title: "How to Fix Vmmem High Memory (RAM) Consumption"
date: 2023-06-26
permalink: /posts/2023-06-26/how-to-fix-vmmem-high-memory-ram-consumption/
categories:
  - Blog
tags:
  - Blog
excerpt: "A Quick Fix to Reduce RAM Usage in Windows with Linux Subsystem"
canonical_url: https://medium.com/@seyyedaliayati/how-to-fix-vmmem-high-memory-ram-consumption-d1320ee75caf
---

### How to Fix Vmmem High Memory (RAM) Consumption

A Quick Fix to Reduce RAM Usage in Windows with Linux Subsystem

Open PowerShell and follow the following steps:

### Step 1
    
    
    wsl --shutdown

### Step 2
    
    
    notepad "$env:USERPROFILE/.wslconfig"

You may get the following warning:

![](https://cdn-images-1.medium.com/max/800/1*9kYeKRZRlZZaTHoL550isg.png)

If so, choose “Yes”.

### Step 3

Add the following to the end of the file and save it:
    
    
    [wsl2]  
    memory=3GB   # Limits VM memory in WSL 2 up to 3GB  
    processors=4 # Makes the WSL 2 VM use two virtual processors

### Step 4

Now, reboot your system!