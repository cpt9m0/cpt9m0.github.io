---
layout: post
title: "Access Control Is All You Need"
date: 2025-04-27
author: Ali Ayati
---

Access control is the foundation of system security. Every operating system, application, and service must enforce who can do what — and this seemingly simple question is surprisingly hard to get right.

I chose "Access Control Is All You Need" as the working title of my PhD thesis because I believe that getting access control right eliminates an enormous class of security vulnerabilities. Buffer overflows, privilege escalation, information disclosure — many of the most damaging attacks succeed because access control policies are missing, incomplete, or incorrectly implemented.

## Why Access Control Matters

Consider the Windows kernel. Its access control model is among the most complex in any shipping OS, with security descriptors, access tokens, mandatory integrity levels, and privilege checks spread across hundreds of system calls. Understanding how these pieces interact is essential for building secure systems on Windows.

In my research, I explore:

- **How access control models evolve** across OS versions and how backward compatibility introduces security gaps
- **Where access control checks are missing** in common software patterns and how to detect these gaps automatically
- **How LLMs can help** analyze and generate access control policies from natural language descriptions

## What's Next

This blog will document my journey through PhD research, system security, kernel development, and the occasional detour into interesting side projects. If you're working on access control, EDR systems, or applying LLMs to security problems, I'd love to hear from you.

Stay tuned for more posts on Windows internals, kernel driver development, and acoustic side-channel attacks.
