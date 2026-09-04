---
title: "About"
layout: gridlay
description: "Learn more about Ali Ayati, his education, systems security research, AI-driven security analysis, projects, and teaching experience."
permalink: /about/
---

# About

<div class="section-card">
<div class="pi-card">
<img src="{{ site.url }}{{ site.baseurl }}/images/{{ site.photo }}" class="pi-photo" alt="{{ site.name }}" loading="lazy">
<div>
<h3 class="pi-name">{{ site.name }}</h3>
<p style="font-style: italic; color: var(--text-secondary);">{{ site.title }}, {{ site.institution }}</p>
<div class="pi-links">
{% if site.email %}<a href="mailto:{{ site.email }}" class="icon-link" title="Email"><i class="fa-solid fa-envelope"></i></a>{% endif %}
{% if site.links.cv and site.links.cv != "" %}<a href="{{ site.url }}{{ site.baseurl }}/{{ site.links.cv }}" class="icon-link" title="CV"><i class="ai ai-cv"></i></a>{% endif %}
{% if site.links.google_scholar and site.links.google_scholar != "" %}<a href="{{ site.links.google_scholar }}" class="icon-link" title="Google Scholar"><i class="ai ai-google-scholar"></i></a>{% endif %}
{% if site.links.github and site.links.github != "" %}<a href="{{ site.links.github }}" class="icon-link" title="GitHub"><i class="fa-brands fa-github"></i></a>{% endif %}
{% if site.links.linkedin and site.links.linkedin != "" %}<a href="{{ site.links.linkedin }}" class="icon-link" title="LinkedIn"><i class="fa-brands fa-linkedin"></i></a>{% endif %}
</div>
<ul style="margin-top: var(--space-4);">
<li>Ph.D. Computer Engineering - Texas A&M University (2023-Expected 2027)</li>
<li>B.S. Computer Engineering - Iran University of Science and Technology (2017-2022)</li>
</ul>
</div>
</div>
</div>

<div class="section-card">
<h3>Education & Research</h3>

<h4>Ph.D. Studies - Texas A&M University (January 2023-Expected December 2027)</h4>
<ul>
<li><strong>GPA:</strong> 3.60/4.00</li>
<li><strong>Research:</strong> Systems security, access control, kernel-level instrumentation, AI-driven security analysis</li>
<li><strong>Coursework:</strong> Software Security, Software Reverse Engineering, Large Language Models, Deep Learning, Operating Systems, Reinforcement Learning</li>
<li><strong>Thesis:</strong> "Access Control Is All You Need"</li>
<li><strong>Advisor:</strong> <a href="https://marcusbotacin.github.io/">Prof. Dr. Marcus Botacin</a></li>
</ul>

<h4>B.S. in Computer Engineering - Iran University of Science and Technology (September 2017-February 2022)</h4>
<ul>
<li><strong>GPA:</strong> 3.50/4.00</li>
<li><strong>Thesis:</strong> CodART: An Automated Refactoring System</li>
<li><strong>Advisor:</strong> Dr. Saeed Parsa</li>
<li><strong>Project:</strong> <a href="https://github.com/m-zakeri/CodART" target="_blank">CodART on GitHub</a></li>
</ul>

<p>See the <a href="{{ site.url }}{{ site.baseurl }}/projects">Projects</a> page for details on current and past work.</p>
</div>

<div class="section-card">
<h3>Teaching</h3>
<p><strong>Graduate Research and Teaching Assistant</strong> - Texas A&M University & Iran University of Science and Technology. See the <a href="{{ site.url }}{{ site.baseurl }}/teaching">Teaching</a> page for the full course list.</p>
</div>

<div class="section-card">
<h3>Experience</h3>

<h4>Software Engineer Intern - Inertia Systems</h4>
<p><em>May 2026 - August 2026</em></p>
<ul>
<li>Focused on full-stack feature delivery, cloud automation, and AI-driven data pipelines.</li>
<li>Parsed Revit (BIM) structural files into a Graph Database to build a RAG pipeline for intelligent contextual search.</li>
<li>Architected a cloud-based background scheduling engine built on PHP, Doctrine ORM, and AWS infrastructure (EventBridge, S3, SSM).</li>
<li>Built a React 19/TypeScript toast engine for the frontend design system, covered by 80+ Vitest unit and accessibility tests.</li>
<li>Optimized database write overhead by 60% and secured multi-tenant data boundaries on batch operations.</li>
<li>Authored 15+ merged PRs, reviewed 10+ teammate PRs, and leveraged AI coding agents under strict code-review and testing standards.</li>
</ul>

<h4>Research Assistant - Botacin's Lab, Texas A&M University</h4>
<p><em>January 2024 - Present</em></p>
<ul>
<li>Architect real-time Windows Endpoint Detection and Response systems with C/C++ kernel-mode drivers for low-latency system call interception.</li>
<li>Designed and benchmarked 7+ prevention algorithms, reducing false positive rates to less than 1.0% while maintaining robust prevention capabilities.</li>
<li>Engineered a parallelized analysis pipeline for 60M+ kernel trace events using hierarchical structures and anomaly detection.</li>
<li>Developed automated evaluation infrastructure across 6,000+ malware and benign samples for prevention metrics including TPR, FPR, and precision.</li>
<li>Built proactive access-control generation techniques that prevent malware execution by enforcing blocking rules before runtime execution.</li>
</ul>

<h4>Back End Engineer - SynApps</h4>
<p><em>January 2022 - December 2022</em></p>
<ul>
<li>Enhanced Django-based backend systems</li>
<li>Designed RESTful API endpoints</li>
<li>Collaborated with distributed teams for feature deployment</li>
</ul>
</div>

<div class="section-card">
<h3>Certifications</h3>
<ul>
<li><strong><a href="https://www.credly.com/badges/dfa3484e-ab86-4bda-84da-173c6b37d452/linked_in_profile" target="_blank" rel="noopener noreferrer">GitHub Foundations</a></strong> - GitHub</li>
<li><strong><a href="https://learn.nvidia.com/certificates?id=1cLSxHE0R0SHlVW6iZE0MQ" target="_blank" rel="noopener noreferrer">Data Parallelism: How to Train Deep Learning Models on Multiple GPUs</a></strong> - NVIDIA</li>
<li><strong><a href="https://learn.nvidia.com/certificates?id=pVK2Ck4OQEiVGgbVnyZvAg" target="_blank" rel="noopener noreferrer">Fundamentals of Accelerated Data Science</a></strong> - NVIDIA</li>
<li><strong>GRAD Aggies Professional Development Certificate</strong> - Texas A&M University</li>
</ul>
</div>

<div class="section-card">
<h3>Skills</h3>
<p><strong>Programming Languages:</strong> Python, C, C++, Java</p>
<p><strong>Databases:</strong> SQL, MongoDB, Redis, Amazon RDS</p>
<p><strong>Systems & Security:</strong> Windows Kernel-Mode Driver Framework, PE files, ANTLR, Ghidra, YARA</p>
<p><strong>Backend & Web:</strong> Django, HTML, CSS, FastAPI, Flask</p>
<p><strong>Machine Learning:</strong> Graph analysis, Random Forest, PyTorch, PEFT, LoRA/QLoRA</p>
<p><strong>DevOps & Tooling:</strong> Git, CI/CD, Docker, Linux</p>
</div>
