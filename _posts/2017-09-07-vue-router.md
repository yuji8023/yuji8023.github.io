---
layout: post
title: vue-router笔记
date: 2017-09-07 23:34:00
categories: blog
tags: [vue]
description: The official router for Vue.js
---

## 安装

    npm install vue-router

如果在一个模块化工程中使用它，必须要通过 Vue.use() 明确地安装路由功能：

    import Vue from 'vue'
    import VueRouter from 'vue-router'
    
    Vue.use(VueRouter)

## <router-link>

<router-link> 组件支持用户在具有路由功能的应用中（点击）导航。通过to属性指定目标地址，默认会被渲染成带有正确链接的 <a> 标签，可以通过配置tag属性生成别的标签.。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的 CSS 类名。

相比于<a href="...">的优势：

* 在`HTML5 history `模式与hash模式表现一致，切换无需变动
* 在`HTML5 history `模式下，`router-link`会阻止拦截点击事件，无需重新加载页面
* 当你在 HTML5 history 模式下使用 base 选项之后，所有的to属性都不需要写（基路径）了。