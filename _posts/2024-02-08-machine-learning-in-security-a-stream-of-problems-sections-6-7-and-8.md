---
title: "Machine Learning (In) Security: A Stream of Problems -- Sections 6, 7, and 8"
date: 2024-02-08
permalink: /posts/2024-02-08/machine-learning-in-security-a-stream-of-problems-sections-6-7-and-8/
categories:
  - Machine Learning
tags:
  - Machine Learning
  - Security
excerpt: "A Summary of what I have learned…"
canonical_url: https://medium.com/@seyyedaliayati/machine-learning-in-security-a-stream-of-problems-sections-6-7-and-8-5fb24da8c720
---

### Machine Learning (In) Security: A Stream of Problems -- Sections 6, 7, and 8

#### A Summary of what I have learned…

In our CSCE 689 ML-Based Cyberdefenses course at Texas A&M University, we’re exploring the latest in cybersecurity with [Dr. Marcus Botacin](https://engineering.tamu.edu/cse/profiles/botacin-marcus.html). Imagine diving into research papers, unraveling how machine learning tackles cyber threats. This blog post is like a snapshot of our class discussions, breaking down complex topics in the world where academia meets real-world challenges.

In my [last blog post](https://medium.com/@seyyedaliayati/machine-learning-in-security-a-stream-of-problems-sections-3-4-and-5-97d0285a77ae), I summarized key challenges highlighted in sections 3–5 of the paper “Machine Learning (In) Security: A Stream of Problems.” Specifically, I covered major data collection pitfalls like temporal data inconsistencies, inaccurate labeling, class imbalance issues, and the dilemma of dataset size definition. In this post, I will summarize Sections 6–8, which dive into additional machine learning problems in model development, evaluation, and understanding the limitations of ML security.

Section 6 examines concept drift and evolution, adversarial attacks, balancing precision and recall, transfer learning drawbacks, and barriers in real-world implementation. Section 7 discusses the need for better evaluation practices — using appropriate metrics for imbalanced data, avoiding apples-to-oranges benchmark comparisons, considering impact of label delays, and distinguishing between online and offline criteria. Finally, Section 8 reiterates that ML models fundamentally remain a type of signature scheme, cautions against claims of 0-day detection abilities, and underscores why explainability matters for security solutions to enable incident response. It concludes by asserting the enduring cat-and-mouse game between evolving attacks and defenses.

![](https://cdn-images-1.medium.com/max/800/1*pCUR5eXz-Na6pdVA9eILTw.png)Figure 1: Different types of concept drift. (Image is taken from the paper)

Our lecture dove into practical issues in applying machine learning to security. A key focus was on concept drift — where the nature of threats evolves over time. As Figure 1 shows, drift may be sudden or gradual. This poses challenges for malware detection, as old training data may not capture new attack types. Techniques like DDM and EDDM aim to detect drift. However, as my classmate highlighted, balancing aggressiveness in updating models with avoiding outdated classifiers is tricky.

We also touched on dealing with the extreme class imbalance common in security. As an alternative to resampling datasets, techniques like adjusting loss functions or cost-sensitive learning reweight classes so minority classes don’t get ignored. I thought the example of malware detection as a use case was apt — a detector blocking Microsoft Word would make a system useless, even if it catches most malwares!

A lightbulb moment for me was realizing machine learning models are essentially weighted signature schemes. As section 8.1 explains, ML models look for combinations of features, unlike traditional signatures. But they still have limitations in zero-day threat detection. This arms race between attacks and defenses will likely continue.

![](https://cdn-images-1.medium.com/max/800/1*1bZhfzQK5D3vaikYin_Xhg.png)Figure 2: A random forest combines the predictions of diverse decision trees, achieving higher accuracy and resilience to overfitting through ensemble learning. [1][2]

Also ensemble methods like Random Forests (Figure 2) were highlighted as helping address non-linearity and concept evolution. As my professor noted, neural networks have downsides here, often “forgetting” old threats. The one-class classifier examples for outlier detection also clicked for me — identifying if a user login departs from normal behavior is clever.

In summary, thought-provoking topics around concept drift, explainability, and real-world evaluation metrics left me wanting to dig deeper. I’m excited to explore solutions that move the needle — perhaps leveraging online learning or active learning to better keep pace with an evolving threat landscape.

#### Main Reference

Ceschin, F., Botacin, M., Bifet, A., Pfahringer, B., Oliveira, L. S., Gomes, H. M., & Grégio, A. (2020). Machine learning (in) security: A stream of problems. Digital Threats: Research and Practice. (<https://arxiv.org/abs/2010.16045>)

Other References

  * [1] <https://www.linkedin.com/pulse/exploring-power-random-forest-from-decision-trees-ensemble-pandey/>
  * [2] Breiman, L. (2001). Random forests. Machine learning, 45, 5–32.