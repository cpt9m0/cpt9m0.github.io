---
title: "About"
layout: gridlay
description: "Learn more about Ali Ayati, his education, systems security research, AI-driven security analysis, projects, and teaching experience."
permalink: /about/
---

## About

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

<h4>Current Projects</h4>
<ul>
<li><strong>Real-time EDR System:</strong> Windows-based Endpoint Detection and Response with C/C++ kernel-mode drivers and proactive access-control generation</li>
<li><strong>EchoCrypt:</strong> LLM-assisted acoustic side-channel analysis framework for noisy keyboard recordings</li>
<li><strong>Interactive Malware Feature Engineering Lab:</strong> Web-based environment for Random Forest malware feature experimentation</li>
</ul>
<p><a href="{{ site.url }}{{ site.baseurl }}/projects" class="btn-pill">View Projects</a></p>
</div>

<div class="section-card">
<h3>Teaching Experience</h3>
<p><strong>Graduate Research and Teaching Assistant</strong> - Texas A&M University</p>

<h4>Graduate Courses:</h4>
<ul>
<li>CSCE 704 - Data Analytics Cybersecurity</li>
<li>CSCE 611 - Graduate Operating Systems</li>
<li>CSCE 413 - Software Security</li>
</ul>

<h4>Undergraduate Courses:</h4>
<ul>
<li>CSCE 482/483 - Senior Capstone Design</li>
<li>CSCE 411 - Design and Analysis of Algorithms</li>
<li>CSCE 410 - Operating Systems</li>
</ul>
</div>

<div class="section-card">
<h3>Experience</h3>

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
<li><strong>GitHub Foundations</strong> - GitHub</li>
<li><strong>Data Parallelism: Training Deep Learning Models on Multiple GPUs</strong> - NVIDIA</li>
<li><strong>Fundamentals of Accelerated Data Science</strong> - NVIDIA</li>
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
