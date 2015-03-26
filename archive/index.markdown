---
layout: page
title: Archive
---

<p>Este es un listado visual de todos los posts que han habido, en orden cronológico descendente:</p>

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}