---
author_profile: false
permalink: /research/
title: "Research interests"
layout: single
header:
  overlay_image: ../images/overlay.jpg
  overlay_filter: 0.2 # same as adding an opacity of 0.5 to a black background
---

{% assign sorted = (site.research | sort: 'date') | reverse %}
{% for research in sorted %}
<h3> {{ research.date | date: '%B %d, %Y' }} <a href="{{ research.url }}">{{ research.title }}</a></h3><br>
<span>{{ research.description }}</span>
{% if research.img %}
<img src="{{ site.baseurl }}/images/{{ research.img }}" width="400">
{% endif %}
{% endfor %}
