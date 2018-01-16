---
layout: post
title: webpack require.ensure
date: 2017-09-08 17:40:00
categories: blog
tags: [vue]
description: 详解vue项目优化之按需加载组件-使用webpack require.ensure
---

## 前言

使用 vue-cli构建的项目,在默认情况下,执行 `npm run build`会将所有的js代码打包为一个整体,打包位置是 `dist/static/js/app.[contenthash].js`  ，影响页面加载。
如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

## require.ensure()

webpack中利用require.ensure()实现按需加载

webpack 在编译时，会静态地解析代码中的 require.ensure()，同时将模块添加到一个分开的 chunk 当中。这个新的 chunk 会被 webpack 通过 jsonp 来按需加载。

语法如下：

    require.ensure(dependencies: String[], callback: function(require), chunkName: String)

+ 依赖 dependencies
  这是一个字符串数组，通过这个参数，在所有的回调函数的代码被执行前，我们可以将所有需要用到的模块进行声明。

+ 回调 callback
  当所有的依赖都加载完成后，webpack会执行这个回调函数。require 对象的一个实现会作为一个参数传递给这个回调函数。因此，我们可以进一步 require() 依赖和其它模块提供下一步的执行。

+ chunk名称 chunkName
  chunkName 是提供给这个特定的 require.ensure() 的 chunk 的名称。通过提供 require.ensure() 不同执行点相同的名称，我们可以保证所有的依赖都会一起放进相同的 文件束(bundle)。

## require.ensure() 的坑点

[链接]（http://blog.csdn.net/qq_27626333/article/details/76228578）

## 使用

    const Province = r => require.ensure([], () => r(require('@/components/Province.vue')), 'chunkname1')
    
    const Segment = r => require.ensure([], () => r(require('@/components/Segment.vue')), 'chunkname2')
    
    const Loading = r => require.ensure([], () => r(require('@/components/Loading.vue')), 'chunkname3')
    
    const User = r => require.ensure([], () => r(require('@/components/User.vue')), 'chunkname3')

根据`chunkame`的不同,上面的四个组件,将会被分成3个块打包,最终打包之后与组件相关的js文件会分为3个 (除了`app.js`,`manifest.js`, `vendor.js`)

