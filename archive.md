---
layout: page
title: "BLOGS"
description: "你看到的，是我所有的博客"
header-img: "img/green.jpggit"
---


<ul class="listing">
{% for post in site.posts %}
  {% capture y %}{{post.date | date:"%Y"}}{% endcapture %}
  {% if year != y %}
    {% assign year = y %}
    <li class="listing-seperator" style="list-style:none;color:#c0c0c0;position:relative;left:-16px;">{{ y }} 年</li>
  {% endif %}
  <li class="listing-item">
    <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
    <time style="float:right;color:#c0c0c0;" datetime="{{ post.date | date:"%Y-%m-%d %H:%M:%S" }}">{{ post.date | date: '%Y-%m-%d %H:%M:%S'}}</time>
  </li>
{% endfor %}
</ul>