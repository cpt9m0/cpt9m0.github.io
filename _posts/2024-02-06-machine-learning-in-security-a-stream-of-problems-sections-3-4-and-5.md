---
title: "Machine Learning (In) Security: A Stream of Problems -- Sections 3, 4, and 5"
subtitle: "Class summary and research insights on ML-based cyberdefenses — Sections 3, 4, and 5"
date: 2024-02-06
permalink: /posts/2024-02-06/machine-learning-in-security-a-stream-of-problems-sections-3-4-and-5/
categories:
  - Machine Learning
  - Security
  - Cybersecurity
tags:
  - Machine Learning
  - Security
  - Cybersecurity
excerpt: "Summary and insights from CSCE 689 ML-Based Cyberdefenses course, focusing on sections 3, 4, and 5."
---

### Machine Learning (In) Security: A Stream of Problems -- Sections 3, 4, and 5

#### A Summary of what I have learned…

In our CSCE 689 ML-Based Cyberdefenses course at Texas A&M University, we’re exploring the latest in cybersecurity with [Dr. Marcus Botacin](https://engineering.tamu.edu/cse/profiles/botacin-marcus.html). Imagine diving into research papers, unraveling how machine learning tackles cyber threats. This blog post is like a snapshot of our class discussions, breaking down complex topics in the world where academia meets real-world challenges.

#### Main Reference

Ceschin, F., Botacin, M., Bifet, A., Pfahringer, B., Oliveira, L. S., Gomes, H. M., & Grégio, A. (2020). Machine learning (in) security: A stream of problems. _Digital Threats: Research and Practice_. (<https://arxiv.org/abs/2010.16045>)

#### Data Collection

Data collection is crucial in cybersecurity machine learning. As discussed in class, data comes in three formats: raw, attributes, and features. Raw data like malware binaries or network traffic provide full information. Attributes are filtered metadata from raw data, like JSON files of extracted characteristics. Features are attributes transformed into model-ready inputs.

#### K-Fold Cross Validation

![](https://cdn-images-1.medium.com/max/800/1*E1e-8OmoqJaSmHxxPXcPGg.png)Figure 1: k-fold (in this case k is 5) cross validation [1]

A common machine learning technique is k-fold cross-validation, splitting data into k partitions for training and testing (Figure 1). But this causes data leakage in cybersecurity by mixing data from different time periods. Temporal consistency is the key — training data should predate testing data. Timestamps matter!

#### Data Labeling

Accurate labeling is challenging too. Relying solely on antivirus engines for malware labels has drawbacks, as their **labels change over time**. Something initially labeled as generic may later get a more specific label. As mentioned in class, techniques like AVClass [2] and Euphony [3] help unify labels from different antiviruses.

#### Class Imbalance

![](https://cdn-images-1.medium.com/max/800/1*sfl4DQsz9w2OZs_GJ1xHdw.png)Figure 2: The importance of temporal information in undersampling

Class imbalance is another problem, with real-world cyber data having far more benign than malicious examples. As Figure 2 illustrates, blind undersampling may improperly remove subclasses. Oversampling like SMOTE risks generating data from the wrong time periods. Both should respect temporal information.

#### Having More Data Is NOT Always Necessary

![](https://cdn-images-1.medium.com/max/800/1*ByJurlRsttS9msl9G8Or_w.png)Figure 3: Dataset size definition in terms of f1score

An interesting point from class is that observational studies on ecosystem landscapes are crucial for useful datasets, instead of just amassing data. The figures (Figure 3) show performance often stabilizes before using full datasets. So we should understand what data **represents** the real-world scenario.

#### Attributes and Features

![](https://cdn-images-1.medium.com/max/800/1*ayt0I9NL8IZtI73Gbza0Gg.png)Figure 4: Accuracy is higly impacted by the type of attributes

Attribute and feature extraction impact models too. Dynamic attributes from running malware generally provide higher accuracy as in Figure 4, but have computational costs. And features must be updated over time as new threats appear, or adversarial attacks could trick models by making malicious features mimic benign ones.

![](https://cdn-images-1.medium.com/max/800/0*4x3ie3hAqBd_T6w5.png)Figure 5: Adversarial Attacks. The deliberate manipulation of machine learning models by introducing carefully crafted input data. [4][5]

The choice of features impacts model robustness against **adversarial attacks (Figure 5)**. Easily modifiable features like raw bytes can be vulnerable, as attackers could append benign data to malware to trick classifiers. Approaches based on control flow graphs are also susceptible if malware structurally modifies itself to mimic legitimate software. Selecting features that are resistant to manipulation, like specific loop instructions, can improve security. Overall, researchers must evaluate feature robustness like attackers to develop resilient ML solutions. The effectiveness of attacks depends on threat models — adjustments benign for one scenario may be malicious in another. Still, thoughtfully engineering features is key to avoid **“garbage in, garbage out”** from adversarial samples.

#### The Key Takeaway

In summary, thoughtfully collecting, labeling, and representing data is vital in cybersecurity machine learning. The key takeaway from class is real-world applicability should drive choices, not just maximizing performance metrics.

#### Other References

  * [1] <https://scikit-learn.org/stable/modules/cross_validation.html>
  * [2] Marcos Sebastián, Richard Rivera, Platon Kotzias, and Juan Caballero. 2016. AVclass: A Tool for Massive Malware Labeling. Springer International Publishing, Cham, 230–253. <https://doi.org/10.1007/978-3-319-45719-2_11>
  * [3] Mederic Hurier, Guillermo Suarez-Tangil, Santanu Kumar Dash, Tegawende F. Bissyande, Yves Le Traon, Jacques Klein, and Lorenzo Cavallaro. 2017. Euphony: Harmonious Unification of Cacophonous Anti-Virus Vendor Labels for Android Malware. In IEEE International Working Conference on Mining Software Repositories. IEEE Computer Society, 425–435. <https://doi.org/10.1109/MSR.2017.57>
  * [4] <https://towardsdatascience.com/breaking-neural-networks-with-adversarial-attacks-f4290a9a45aa>
  * [5] <https://towardsdatascience.com/breaking-neural-networks-with-adversarial-attacks-f4290a9a45aa>