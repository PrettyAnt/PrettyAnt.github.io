---
layout: page
title: About
description: 笑一笑，十年少。你笑的样子好猥琐。。。哈哈哈。。。
share: false
comments: true
canvas: true
menu: 关于
permalink: /about/
---

<div>
  <h3>我的联系方式</h3>
  <ul style="line-height: 3rem;list-style-type: none;">
    {% for obj in site.data.social %}
    <li>
      <img width="32" height="32" style="margin-right:0.375rem;vertical-align: middle;" src="{{ obj.svg }}"/>&nbsp;
      <a href="{{ obj.url }}" title="{{ obj.title }}" style="white-space:pre"><code>{{ obj.sitename }}</code> {{ obj.name }}</a>
    </li>
    {% endfor %}
  </ul>
  <h3>About me</h3>
  <ul style="list-style-type: none;">
    {% for skill in site.data.skills %}
      <li>
        <h4>{{ skill.name }}</h4>
        <div class="btn-inline">
          {% for keyword in skill.keywords %}
            <button class="btn btn-outline" type="button">{{ keyword }}</button>
          {% endfor %}
        </div>
      </li>
    {% endfor %}
  </ul>
</div>