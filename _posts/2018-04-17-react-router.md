---
layout: post
title: React Router
date: 2018-04-04 09:12:00
categories: blog
tags: [React]
description: React Router 是一个基于 React 之上的强大路由库
---

## 获取 URL 参数

+ 动态路由

    // 来自于路径 `/inbox/messages/:id`
    const id = this.props.params.id

+ 路由参数

~~~
    // 来自于路径 
    /inbox/messages/foo?bar=baz
    this.props.location.query.bar
~~~

让 UI 从 URL 中解耦出来:绝对路径
兼容旧的 URL：`<Redirect from="messages/:id" to="/messages/:id" />`

## 进入和离开的Hook
Route 可以定义 `onEnter` 和 `onLeave` 两个 hook ，这些hook会在页面跳转确认时触发一次。这些 hook 对于一些情况非常的有用，例如权限验证或者在路由跳转前将一些数据持久化保存起来。

在路由跳转过程中，onLeave hook 会在所有将离开的路由中触发，从最下层的子路由开始直到最外层父路由结束。然后onEnter hook会从最外层的父路由开始直到最下层子路由结束。

继续我们上面的例子，如果一个用户点击链接，从 `/messages/5` 跳转到` /about`，下面是这些 `hook` 的执行顺序：

    /messages/:id 的 onLeave
    /inbox 的 onLeave
    /about 的 onEnter

### 替换的配置方式:JSX与route数组对象

## 路由匹配原理

+ 嵌套关系

React Router 会深度优先遍历整个路由配置来寻找一个与给定的 URL 相匹配的路由。

+ 路径匹配

路由路径是匹配一个（或一部分）URL 的 一个字符串模式。除了以下情况
`:paramName` – 匹配一段位于 /、? 或 # 之后的 URL。 命中的部分将被作为一个参数
`()` – 在它内部的内容被认为是可选的
`*` – 匹配任意字符（非贪婪的）直到命中下一个字符或者整个 URL 的末尾，并创建一个 `splat` 参数

+ 优先级

### history三种模式

你可以从 React Router 中引入它们：

    // JavaScript 模块导入（译者注：ES6 形式）
    import { browserHistory } from 'react-router'

然后将它们传递给<Router>:

    render(
      <Router history={browserHistory} routes={routes} />,
      document.getElementById('app')
    )

服务器端配置：对所有的页面请求返回index.html文件

IE8/9的支持情况：

    当浏览器不支持window.location的时候，路由跳转会导致全页面刷新，并不会回退到hash模式

**hashHistory：**

    使用hash（#）的方式去创建路由，不需要配置即可运行
**createMemoryHistory**
     
    Memory history 不会在地址栏被操作或读取。
    和另外两种history的一点不同是你必须创建它，这种方式便于测试。

    const history = createMemoryHistory(location)


**默认路由（IndexRoute）**

Index Links
<Link to="/">Home</Link> 会一直处于激活状态
使用<IndexLink to="/">Home</IndexLink>替代

生命周期钩子 `routerWillLeave`

## 服务端渲染

使用 match 在渲染之前根据 location 匹配 route
使用 RoutingContext 同步渲染 route 组件

## 路由组件生命周期与react组件一样

## 在组件外部使用导航
在组件内部可以使用 this.context.router 来实现导航
Router组件上被赋予的history可以在组件外部实现导航。

    import { browserHistory } from 'react-router'
    browserHistory.push('/some/path')

### 如何获得上一次路径

    componentWillReceiveProps(nextProps) {
        const routeChanged = nextProps.location !== this.props.location
        this.setState({ showBackButton: routeChanged })
    }

