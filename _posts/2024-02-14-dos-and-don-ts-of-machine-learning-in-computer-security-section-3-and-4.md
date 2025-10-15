---
title: "Dos and Don’ts of Machine Learning in Computer Security -- Section 3 and 4"
subtitle: "Sampling bias, data snooping, and impact analysis in ML-based cybersecurity — Sections 3 and 4 summary"
date: 2024-02-14
permalink: /posts/2024-02-14/dos-and-don-ts-of-machine-learning-in-computer-security-section-3-and-4/
categories:
  - Machine Learning
  - Security
  - Cybersecurity
tags:
  - Machine Learning
  - Security
  - Cybersecurity
excerpt: "Summary of sampling bias, data snooping, and impact analysis in ML-based cybersecurity, based on CSCE 689 course."
---

### Dos and Don’ts of Machine Learning in Computer Security -- Section 3 and 4

#### A Class Summary of CSCE 689 ML-Based Cybersecurity Course Taught by Dr. Marcus Botacin at Texas A&M University

In [my last blog post](https://medium.com/@seyyedaliayati/dos-and-donts-of-machine-learning-in-computer-security-section-2-e4780dbed530), I explored the pitfalls of machine learning in computer security based on the section 2 of Daniel Arp’s paper, “[Dos and Don’ts of Machine Learning in Computer Security.](https://www.usenix.org/system/files/sec22summer_arp.pdf)” The article highlights the necessity of a robust threat model and delves into the vulnerabilities of both white-box and black-box models, emphasizing the challenges posed by evasion attacks. It also addresses label shifting, where changing relationships between data features and labels can impact model predictions. The key takeaway is the significance of understanding and addressing these pitfalls to harness the full potential of machine learning for a safer digital world.

In this blog post, we continue with sections 3 and 4, and will explore striking statistics revealing the scale of sampling bias (see Appendix 1) and data snooping (see Appendix 2) in published papers as well as themes from the class discourse contrasting different detection approaches.

#### Section 3 — Prevalence Analysis

![](https://cdn-images-1.medium.com/max/800/1*V-kk6u0C2dkPdYXIkDxE-g.png)Figure 1: The pitfalls suffered by each of the 30 papers analyzed. The colors of each bar show the degree to which a pitfall was present, and the width shows the proportion of papers in that group. The number at the center of each bar shows the cardinality of each group. (Image is taken from the paper)

The prevalence analysis reveals concerning trends in security research. As Figure 1shows, 90% of papers exhibit sampling bias and 73% demonstrate data snooping. This indicates a lack of rigor and awareness of pitfalls, attributing it to the complexity of cybersecurity and a focus on showcasing results. However, as the in-class discussion emphasized, these pitfalls obstruct progress.

We must address the rapid evolution of threats with continual enhancement of methods rather than one-off publications. The analysis forces us to confront the underlying issues to enable meaningful advances. For instance, the challenges in collecting representative data, highlighted in the discussion on attribution, manifest as sampling bias. As young researchers, we must question assumptions and challenge the status quo.

#### Section 4 — Impact Analysis

![](https://cdn-images-1.medium.com/max/800/1*CZPoIHxDwi5jfcV7rvKX_w.png)Table 1: Comparison of results for two classifiers when merging benign apps from GooglePlay with Chinese malware (D1) vs. sampling solely from GooglePlay (D2). (Table is taken from the paper)

The impact analysis quantitatively demonstrates how subtle errors produce misleading conclusions. For example, Table 1 shows malware detection performance drops by up to 16.9% due to sampling bias. Figure 2 reveals artifacts in attribution data cause a 48% accuracy drop.

![](https://cdn-images-1.medium.com/max/800/1*RaHf0xmXCQ0L8k-VZQ0_4g.png)Figure 2: Accuracy of authorship attribution after considering artifacts. The accuracy drops by 48 % if unused code is removed from the test set (T1); After retraining (T2), the average accuracy still drops by 6 % and 7 %. (Image is taken from the paper)

These case studies cover the key areas from class — vulnerability discovery, attribution, detection approaches. In vulnerability discovery, we see how learning may exploit coding pattern artifacts rather than meaningful features. This connects to the discussion contrasting signature-based and behavior-based detection. The former tends to exploit artifacts, while the latter focuses on anomalies.

Overall, section 4 aligns with the central theme from class: progress necessitates awareness. Whether challenging assumptions in problem formulation or designing rigorous experiments, we must remain cognizant of pitfalls. The impact analysis compels us to build security systems that address the underlying issues rather than exploiting artifacts.

As early-career researchers, we must lead this shift towards meaningful advances even if it means starting small. With concerted efforts, we can transform security research to confront existing challenges.

#### Appendix 1 — Sampling Bias

Sampling bias refers to systematic errors in how a sample is collected from a population, leading to an unrepresentative dataset that does not reflect the true distribution.

For instance, suppose we want to estimate the average height of students at our university. If we only sample students from the basketball and football teams for our study, we would likely overestimate the average height compared to sampling randomly across all students. This occurs because athletes in certain sports tend to be taller than the general student population. Our sampling method would thus be biased towards taller students and produce skewed results. This demonstrates how sampling bias can arise if particular groups are inadvertently overrepresented or underrepresented due to a non-random, biased sampling technique. Even if our sample size is large, sampling bias can still lead us to the wrong conclusions about the population we want to study. Careful sampling design is therefore crucial for collecting representative, unbiased datasets.

#### Appendix 2 — Data Snooping

Data snooping refers to the exploitation of patterns in data that arise simply by chance and not due to an underlying causal relationship.

For example, suppose we are analyzing factors that impact student test scores. By testing a large number of hypotheses about how different parameters like demographics, class size, teaching methods etc. affect scores, we may eventually find a spurious correlation just by coincidence. Data snooping would be concluding that there is a true causal association when there is none in reality.