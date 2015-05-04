---
layout: index
title: Musing
---
# {{ page.title }}

{% for post in site.posts %}

- [{{ post.title }}]({{ post.url }})

{% endfor %}
