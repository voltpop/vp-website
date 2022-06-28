---
layout: single
permalink: "/blogs/"
title: "Fortune favors the bold"
classes: wide

---
{% assign sorted = site.blogs | sort: 'date' | reverse %}
{% for item in sorted %}
  <h2><a href="{{ item.url }}">{{ item.title }}</a></h2>
  <p>{{ item.excerpt | markdownify | strip_html }}</p>
{% endfor %}
