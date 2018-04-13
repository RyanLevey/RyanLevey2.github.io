---
layout: archive
permalink: /machine-learning/
title: "Projects by Tags"
author_profile: true
header:
  image: "images/Milan.jpg"
---

{% include base_path%}
{% include group-by-array
collection=site.post filed="tags" %}

{% for tag in group_names %}
  {% assign post =
  group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}"
  class="archive_subtitle">{{ tag }}</h2>
  {% for post in post %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
