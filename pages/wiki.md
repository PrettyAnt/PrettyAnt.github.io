---
layout: page
title: Wiki
description: 如果你不觉得一年前的自己是个蠢货，那么说明你这一年没什么进步
menu: 维基
repositories: false
share: false
comments: false
categories: true
calendar: true
canvas: true
permalink: /wiki/
---

{% assign sorted_categories = site.wiki | map: "categories" | sort | uniq %}
{% for category in sorted_categories %}
  <h3 name="{{ category }}" id="{{ category}}">{{ category }}</h3>
  <ol class="posts-list">
    {% for article in site.wiki %}
      {% for article_category in article.categories %}
        {% if category == article_category %}
          <li class="posts-list-item"><a class="posts-list-name" href="{{ article.url }}">{{ article.title }}</a></li>
        {% endif %}
      {% endfor %}
    {% endfor %}
  </ol>
{% endfor %}