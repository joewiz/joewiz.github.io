---
layout: page
title: Tags
permalink: /tags/
---

{% assign sorted_tags = site.tags | sort %}
<ul>
  {% for tag in sorted_tags %}
    <li><a href="/tag/{{ tag[0] | replace: ' ', '-' }}/">{{ tag[0] }}</a> ({{ tag[1].size }})</li>
  {% endfor %}
</ul>
