---
title: "Dos and Don’ts of Machine Learning in Computer Security -- Section 2"
subtitle: "Pitfalls and recommendations for ML in cybersecurity — Section 2 summary"
date: 2024-02-13
permalink: /posts/2024-02-13/dos-and-don-ts-of-machine-learning-in-computer-security-section-2/
categories:
  - Machine Learning
  - Security
  - Cybersecurity
tags:
  - Machine Learning
  - Security
  - Cybersecurity
excerpt: "Summary of key pitfalls and recommendations for machine learning in cybersecurity, based on CSCE 689 course."
---

### Dos and Don’ts of Machine Learning in Computer Security -- Section 2

#### Watch Out for These Machine Learning Pitfalls in Cybersecurity! A Class Summary of CSCE 689 ML-Based Cybersecurity Course Taught by Dr. Marcus Botacin atTexas A&M University

Machine learning holds immense potential to revolutionize cybersecurity, but its journey is riddled with hidden traps. The paper [“Dos and Don’ts of Machine Learning in Computer Security”](https://www.usenix.org/conference/usenixsecurity22/presentation/arp) serves as a guide, illuminating these pitfalls and offering crucial recommendations for researchers to navigate them effectively.

![](https://cdn-images-1.medium.com/max/800/1*-phNfM53j-tfughIUCbQQw.png)Figure 1: Common pitfalls of machine learning in computer security (Image is taken from the paper)

The paper meticulously dissects different stages of the machine learning workflow, from data collection and labeling to system design and deployment. Each stage harbors its own set of pitfalls, and the paper delves into their impact on the security domain. Figure 1 provides a visual map of these pitfalls, offering a quick reference for security practitioners.

One crucial pitfall highlighted in the paper is the lack of a robust threat model. Just like any system, machine learning models have vulnerabilities and attack surfaces. Understanding these vulnerabilities through a threat model is paramount. For example, black-box models deployed as APIs might be susceptible to attackers making multiple queries to exploit them.

![](https://cdn-images-1.medium.com/max/800/1*V58Y2ZF21EGXITG-78JS1Q.png)Figure 2: Only the precision-recall curve conveys the true performance (Image istaken from the paper)

The paper also explores the contrasting worlds of white-box and black-box models. White-box models, where all internal workings are transparent, are easier to understand but also more vulnerable to attacks. Black-box models, with their opaque nature, pose a different challenge: understanding how they arrive at their decisions. Both types of models are susceptible to evasion attacks, where attackers manipulate inputs to achieve desired outputs. Figure 2 showcases how different performance metrics can mask the true effectiveness of a model against attacks.

Another pitfall to be wary of is label shifting. This occurs when the relationship between data features and their labels changes over time, leading the model to make incorrect predictions. Imagine training a model to identify spam emails based on specific keywords. If attackers shift their tactics and start using different keywords, the model’s performance will plummet.

These pitfalls doesn’t limit its scope to software systems. It also touches upon the critical concept of hardware security in the context of cyber-physical systems, where malware can cause physical damage. This emphasizes the need for holistic security that encompasses both the digital and physical realms.

While the paper offers valuable insights, it is worth mentioning that the class discussion went beyond the paper’s scope. Exploring these additional topics and delving deeper into the mentioned pitfalls would further enrich my understanding of machine learning security.

Remember, the road to secure machine learning is paved with both potential and pitfalls. By understanding these pitfalls and adhering to the guidance provided in this paper, we can unlock the true potential of machine learning to create a safer digital world.