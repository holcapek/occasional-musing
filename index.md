---
layout: index
title: Musing
---
# {{ page.title }}

{% for post in site.posts %}

- [{{ post.title }}]({{ site.baseurl }}{{ post.url }})

{% endfor %}
