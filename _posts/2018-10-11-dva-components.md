---
layout: post
title: dva项目组件的设计
date: 2018-10-11 09:12:00
categories: blog
tags: [dva]
description: dva中的React组件设计的概念与方法
---

在dva文档的[组件设计方法](https://github.com/dvajs/dva-docs/blob/master/v1/zh-cn/tutorial/04-%E7%BB%84%E4%BB%B6%E8%AE%BE%E8%AE%A1%E6%96%B9%E6%B3%95.md)中介绍了两种组件设计：

+ Container Component 容器组件： 一般指的是具有`监听数据行为`的组件, 也就是绑定`model`数据的组件。以数据容器的角色包含其它子组件，通常在项目中表现出来的类型为：Layouts、Router Components 以及普通 Containers 组件。
```javascript
import React, { Component, PropTypes } from 'react';

// dva 的 connect 方法可以将组件和数据关联在一起
import { connect } from 'dva';

// 组件本身
const MyComponent = (props)=>{};
MyComponent.propTypes = {};

// 监听属性，建立组件和数据的映射关系
function mapStateToProps(state) {
  return {...state.data};
}

// 关联 model
export default connect(mapStateToProps)(MyComponent);
```
+ Presentational Component 展示组件： 不会关联订阅`model`上的数据，所需数据通过`props`传递到组件内部。
```javascript
import React, { Component, PropTypes } from 'react';

// 组件本身
// 所需要的数据通过 Container Component 通过 props 传递下来
const MyComponent = (props)=>{}
MyComponent.propTypes = {};

// 并不会监听数据
export default MyComponent;
```
这两个组件除了关联model的区别以外，设计思路也不同：
`Presentational Component`组件是独立的、纯粹的。组件月业务是解耦的，只是完成自己的独立任务，数据通过props传递，操作通过接口暴露。
`Container Component`组件比较类似月状态管理器，他表现为一个容器，订阅子组件需要的数据，组织子组件的交互逻辑与展示。


在项目实际应用中还可以细分为：展示组件components, 控件controls，管理组件（容器，连接=.=组件）containers，和路由组件routes。

+ components 定义为无状态的用来组合组件的纯函数形式
+ controls 定义为自有状态，生命周期，控制自身渲染的类形式
+ containers 定义为连接数据，负责管理调用复用级别组件，并和其他containers通信
+ routes 就是特殊的containers，和路由有着一对一的映射关系，无法复用

containers负责渲染（调用）展示组件，数据可以来自于model和state，其实不管是setState还是dispatch本质都是一样的，可以相互转换，建议一些非组件共享的数据建议放在state里，可能不那么直观但是减少了代码量，还有异步数据建议放到model里，发挥dva的对异步数据的操控优势，但是有的时候页面上存在大量依赖异步数据的组件，我建议还是使用controls，也是为了减少代码量

除了routes，其他都具有天然的复用属性，将不同层级的特性封装，依次是组合关系


区分组件的目的是为了让数据处理更加集中，让组件高内聚低耦合，降低维护成本。
而不是一种绝对的规则。