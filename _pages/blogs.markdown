---
layout: collection
permalink: "/blogs/"
title: "Headaches with Pictures"
collection: blogs
classes: wide

---
{% assign sorted = site.blogs | sort: 'date' | reverse %}
{% for item in sorted %}
  <a href="{{ item.url }}"><h2>{{ item.title }}</h2></a>
  <p>{{ item.excerpt }}</p>
{% endfor %}
