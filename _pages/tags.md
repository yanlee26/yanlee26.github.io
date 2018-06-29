---
layout: archive
permalink: /tags/
title: "Posts by Tags"
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: DL.jpg
  cta_url: "https://github.com/yanlee26/yanlee26.github.io/"
  caption: "Photo credit: [**yanlee26**](https://yanlee26.github.io/)"
---

{% include base_path %}
{% include group-by-array collection=site.posts field="tags" %}

{% for tag in group_names %}
{% assign posts = group_items[forloop.index0] %}

  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
