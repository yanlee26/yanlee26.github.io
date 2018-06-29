---
layout: archive
permalink: /categories/
title: "Posts by Categories"
author_profile: true
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: DL.jpg
  cta_url: "https://github.com/yanlee26/yanlee26.github.io/"
  caption: "Photo credit: [**yanlee26**](https://yanlee26.github.io/)"
---

{% include base_path %}
{% include group-by-array collection=site.posts field="categories" %}

{% for category in group_names %}
{% assign posts = group_items[forloop.index0] %}

  <h2 id="{{ category | slugify }}" class="archive__subtitle">{{ category }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
