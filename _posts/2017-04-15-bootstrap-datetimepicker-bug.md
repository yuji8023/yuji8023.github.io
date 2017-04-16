---
layout: post
title: bootstrap-datetimepicker的bug
date: 2017-04-15 23:12:00
categories: blog
tags: [bug]
description: bootstrap-datetimepicker在火狐下报错的问题
---

## 前言

在项目中需要用到日期插件，刚好在用bootstrap框架，本着成套使用的想法就选择了bootstrap-datetimepicker。
本来一切都是很美好的，直到测试同学给我提了一个bug。
就是这个问题，虽然很奇怪这么成熟的一个框架的插件居然会有这种不兼容的问题，但bug还是要修复的。

## bug详情

使用bootstrap-datetimepicker这个日期插件来显示日期，其他浏览器显示正常，但在火狐下报如下错误：

`TypeError: (intermediate value).toString(...).split(...)[1] is undefined`

## 解决方法

需要到插件里面改动一句源码：

找到`this.defaultTimeZone=(new Date).toString().split("(")[1].slice(0,-1)`改为`this.defaultTimeZone='GMT '+(new Date()).getTimezoneOffset()/60;`

就可以了

### 参考文章

[技术链接](http://www.cnblogs.com/lhyhappy65/p/5629630.html)
