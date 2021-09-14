---
layout: single
permalink: "/blogs/"
title: "Headaches with Pictures"
classes: wide

---
{% assign sorted = site.blogs | sort: 'date' | reverse %}
{% for item in sorted %}
  <h2><a href="{{ item.url }}">{{ item.title }}</a></h2>
  <p>{{ item.excerpt | markdownify | strip_html }}</p>
{% endfor %}
