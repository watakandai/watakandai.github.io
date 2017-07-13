---
permalink: /trips/
title: Tours and trips
header:
  overlay_image: ../images/dst2_background.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
---

{% for trip in site.trips %}
  <div class="trip">
    <h2><a href="{{ trip.url }}">{{ trip.title }}</a></h2>
  </div>
{% endfor %}
