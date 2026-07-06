---
layout: post
title: "Access Control Is All You Need"
date: 2025-04-27
author: Ali Ayati
description: "Why getting access control right eliminates whole classes of security vulnerabilities, and how my PhD research approaches it through Windows kernel internals and LLMs."
---

Access control is the part of system security I keep coming back to. Every operating system, application, and service has to decide who is allowed to do what, and getting that decision right is a lot harder than it sounds.

I picked "Access Control Is All You Need" as the working title of my PhD thesis because I think most of the damage in security traces back to it. Buffer overflows, privilege escalation, information disclosure: a lot of the worst attacks work because an access control policy was missing, incomplete, or implemented wrong in the first place.

## Why access control matters

Take the Windows kernel. Its access control model is one of the most complicated in any shipping OS, with security descriptors, access tokens, mandatory integrity levels, and privilege checks scattered across hundreds of system calls. You can't build something secure on Windows without understanding how those pieces fit together, and most of the time they don't fit the way the documentation implies.

My research digs into three questions:

- how access control models change across OS versions, and where backward compatibility quietly opens security gaps
- where access control checks go missing in common software patterns, and how to find those gaps automatically
- how LLMs can help analyze and generate access control policies from plain-language descriptions

## What's next

I'm using this blog to write up what I run into: PhD research, system security, kernel work, and whatever side projects pull me off course. If you work on access control, EDR systems, or applying LLMs to security problems, get in touch. More soon on Windows internals, kernel driver development, and acoustic side-channel attacks.
