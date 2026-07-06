---
title: "News"
layout: gridlay
description: "News and updates from Ali Ayati — publications, talks, and milestones in systems security and AI-driven security research."
permalink: /allnews.html
---

# News

<div class="section-card" markdown="0">
<div class="news-timeline">
{% for article in site.data.news %}
<div class="news-item">
<span class="news-date">{{ article.date }}</span>
<span class="news-headline">{{ article.headline }}</span>
</div>
{% endfor %}
</div>
</div>
