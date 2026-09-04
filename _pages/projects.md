---
title: "Projects"
layout: gridlay
description: "Selected systems security, machine learning, and infrastructure projects by Ali Ayati."
permalink: /projects/
---

# Projects

<div class="section-card">
<h3>Real-time Windows EDR System</h3>
<p><em>January 2024 - Present</em></p>
<ul>
<li>Architected a real-time Windows Endpoint Detection and Response system with C/C++ kernel-mode drivers for low-latency system call interception.</li>
<li>Designed and benchmarked 7+ prevention algorithms, reducing false positive rates to less than 1.0% while maintaining robust prevention capabilities.</li>
<li>Engineered a parallelized analysis pipeline for 60M+ kernel trace events using hierarchical structures and anomaly detection.</li>
<li>Developed automated evaluation infrastructure across 6,000+ malware and benign samples for prevention metrics including TPR, FPR, and precision.</li>
<li>Built proactive access-control generation techniques that prevent malware execution by enforcing blocking rules before runtime execution.</li>
</ul>
</div>

<div class="section-card">
<h3>EchoCrypt: High-Performance Side-Channel Analysis Framework</h3>
<p><em>August 2024 - March 2025</em></p>
<p><a href="https://github.com/Botacin-s-Lab/EchoCrypt" target="_blank">GitHub repository</a></p>
<ul>
<li>Developed a modular acoustic side-channel attack framework integrating Vision Transformers, Swin Transformers, and large language models for keystroke recovery in noisy environments.</li>
<li>Optimized inference and evaluation pipelines to achieve 90%+ recovery accuracy across unconstrained recordings.</li>
<li>Implemented LoRA fine-tuning for LLaMA models, reducing memory requirements by approximately 90% while retaining strong contextual correction performance.</li>
<li>Built reproducible PyTorch and CUDA data augmentation and benchmarking pipelines across diverse Zoom and phone audio datasets.</li>
</ul>
</div>

<div class="section-card">
<h3>Interactive Malware Feature Engineering Lab</h3>
<p><em>October 2024 - November 2025</em></p>
<p><a href="https://lab.ali-ayati.com" target="_blank">lab.ali-ayati.com</a></p>
<ul>
<li>Architected and deployed a containerized web application with Docker, Nginx, and Gunicorn for real-time malware feature experimentation.</li>
<li>Engineered a Django backend for budget-constrained Random Forest training jobs over 33+ static PE features.</li>
<li>Designed an interactive interface that provides real-time feature-importance feedback and makes malware analysis workflows more accessible.</li>
<li>Configured production deployment components to support concurrent user sessions and iterative model training.</li>
</ul>
</div>
