---
layout: page
title: Archivo
---

<p>Este es un listado visual de todos los posts que han habido, en orden cronológico descendente:</p>

{% assign year = 0 %}
<dl class="dl-horizontal">
{% for post in site.posts %}
    {% assign newYear = post.date | date: '%Y - %B' %}
    {% if year != newYear %}
        {% assign year = post.date | date: '%Y - %B' %}
        <dt>{{ post.date | date: '%B %Y' }}</dt>
    {% endif %}
    <dd>&raquo; <a href="{{ post.url }}">{{ post.title }}</a></dd>
{% endfor %}
</dl>

