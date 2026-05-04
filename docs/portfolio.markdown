---
layout: default
title: Portfolio
permalink: /portfolio/
---

<article class="post-content portfolio-content">
{% capture portfolio_body %}{% include_relative _posts/portfolio.md %}{% endcapture %}
{{ portfolio_body | markdownify }}
</article>
