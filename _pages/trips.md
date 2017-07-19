---
permalink: /trips/
title: Tours and trips
header:
  overlay_image: ../images/dst2_background.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
---

I enjoy bicycle touring. Here are some trip reports. 

<ul>
{% for trip in site.trips %}
    <li>{{ trip.date }} <a href="{{ trip.url }}">{{ trip.title }}</a></h2>
{% endfor %}
</ul>
