{% for trip in site.trips %}
  <h2>{{ trip.title }}</h2>
  {{ trip.content }}
{% endfor %}
