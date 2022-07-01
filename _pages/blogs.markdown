---
layout: single
permalink: "/blogs/"
title: "Fortune may favor the bold, but success favors the persistent."
classes: wide

---
{% assign sorted = site.blogs | sort: 'date' | reverse %}
{% for item in sorted %}
  <h2><a href="{{ item.url }}">{{ item.title }}</a></h2>
  <p>{{ item.excerpt | markdownify | strip_html }}</p>
{% endfor %}
