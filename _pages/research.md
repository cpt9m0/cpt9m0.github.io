---
title: "Research"
layout: gridlay
description: "Explore Ali Ayati's research in systems security, access control, Windows kernel instrumentation, scalable malware analysis, and LLM-assisted cybersecurity."
permalink: /research/
---

# Research

<div class="section-card">
<h3>System Access Control</h3>
<p>My primary research focuses on access control mechanisms for proactive malware prevention. I investigate how system behavior can be modeled, converted into enforceable policies, and used to block malicious execution paths before runtime compromise.</p>
<p>This work connects operating-system policy enforcement, kernel-level monitoring, and scalable behavioral analysis to provide strong security guarantees while maintaining low false positive rates.</p>
</div>

<div class="section-card">
<h3>Endpoint Detection and Response</h3>
<p>I develop real-time Windows-based Endpoint Detection and Response (EDR) systems that operate at the kernel level. This work uses C/C++ kernel-mode drivers for low-latency system call interception and high-performance threat monitoring.</p>
<p>My current research includes benchmarking prevention algorithms, analyzing large kernel trace datasets, and building automated evaluation frameworks for malware prevention metrics such as TPR, FPR, precision, and robustness across diverse samples.</p>
<p><a href="{{ site.url }}{{ site.baseurl }}/projects">See the Real-time EDR System on the Projects page</a></p>
</div>

<div class="section-card">
<h3>Scalable Security Data Analysis</h3>
<p>I build analysis pipelines for large-scale security datasets, including parallelized processing of 60M+ kernel trace events. These pipelines use hierarchical data structures, graph analysis, and anomaly detection to identify discriminative access patterns at scale.</p>
</div>

<div class="section-card">
<h3>Acoustic Side-Channel Attacks</h3>
<p>My work on acoustic side-channel attacks (ASCAs) explores the viability of recovering keystrokes from recordings of keyboard clicks in noisy, real-world environments. By combining traditional signal processing techniques with large language models for post-processing correction, we have demonstrated state-of-the-art accuracy for ASCAs under unconstrained recording conditions.</p>
<ul>
<li><strong>Key result:</strong> LLM-assisted correction of noisy spectrogram transcripts significantly improves keystroke recovery accuracy in real-world conditions</li>
<li><strong>Published at:</strong> USENIX WOOT '25</li>
</ul>
<p><a href="{{ site.url }}{{ site.baseurl }}/projects">See EchoCrypt on the Projects page</a></p>
</div>

<div class="section-card">
<h3>LLMs in Cybersecurity</h3>
<p>I explore applications of large language models in cybersecurity, including:</p>
<ul>
<li>Automated vulnerability detection and analysis</li>
<li>Security policy generation and verification</li>
<li>Assistance in reverse engineering and binary analysis</li>
<li>Natural language interfaces for security monitoring and incident response</li>
</ul>
</div>

<div class="section-card">
<h3>Reverse Engineering & Software Security</h3>
<p>My research in reverse engineering covers both static and dynamic analysis techniques for understanding software behavior, identifying vulnerabilities, and developing defensive measures. I work with Windows PE binaries, kernel-mode drivers, and embedded systems firmware.</p>
</div>
