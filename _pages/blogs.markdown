---
layout: collection
permalink: "/blogs/"
title: "Headaches with Pictures"
collection: blogs
classes: wide

---
{% assign sorted = site.blogs | sort: 'date' | reverse %}
{% for item in sorted %}
  <h1>{{ item.title }}</h1>
{% endfor %}
