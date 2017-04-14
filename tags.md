---
layout: page
title: "Tags"
description: "哈哈，你找到了我的文章基因库"  
header-img: "img/semantic.jpg"  
---

## 本页使用方法

1. 在下面选一个你喜欢的词
2. 点击它
3. 相关的文章会「唰」地一声跳到页面顶端
4. 马上试试？

## 基因列表


<div id='tag_cloud'>
{% for tag in site.tags %}
<a class="yu-tag" href="#{{ tag[0] }}" title="{{ tag[0] }}" rel="{{ tag[1].size }}">{{ tag[0] }}</a>
{% endfor %}
</div>

<ul class="listing">
{% for tag in site.tags %}
  <li class="listing-seperator" style="list-style:none;color:#c0c0c0;position:relative;left:-16px;" id="{{ tag[0] }}">{{ tag[0] }}</li>
{% for post in tag[1] %}
  <li class="listing-item">
  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
  <time style="float:right;color:#c0c0c0;" datetime="{{ post.date | date:"%Y-%m-%d %H:%M:%S" }}">{{ post.date | date: "%Y-%m-%d %H:%M:%S" }}</time>
  </li>
{% endfor %}
{% endfor %}
</ul>

<script src="/media/js/jquery.tagcloud.js" type="text/javascript" charset="utf-8"></script> 
<script language="javascript">
$.fn.tagcloud.defaults = {
    size: {start: 1, end: 1, unit: 'em'},
      color: {start: '#f8e0e6', end: '#ff3333'}
};

$(function () {
    $('#tag_cloud a').tagcloud();
});
</script>
