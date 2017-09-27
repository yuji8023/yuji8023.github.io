---
layout: post
title: vue-router-config
date: 2017-09-11 21:54:00
categories: blog
tags: [vue]
description: The official router for Vue.js
---

vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

如果不想要很丑的 hash，我们可以用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。

    const router = new VueRouter({
      mode: 'history',
      routes: [...]
    })

**不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 http://oursite.com/user/id 就会返回 404，这就不好看了。**

**所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果URL匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。**

地址：<a href="https://router.vuejs.org/zh-cn/essentials/history-mode.html">https://router.vuejs.org/zh-cn/essentials/history-mode.html</a>

## 警告

给个警告，因为这么做以后，你的服务器就不再返回404错误页面，因为对于所有路径都会返回index.html文件。为了避免这种情况，你应该在Vue应用里面覆盖所有的路由情况，然后在给出一个 404 页面。

const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
或者，如果你是用Node.js作后台，可以使用服务端的路由来匹配URL，当没有匹配到路由的时候返回 404，从而实现 fallback。

**个人理解**：也就是，即使匹配不到路由，服务端也应该返回index.html页面（当前根节点页面），然后前端路由覆盖所有情况，所有没有配置的路由皆由"*"匹配并返回404组件。

## 导航钩子

正如其名，vue-router 提供的导航钩子主要用来拦截导航，让它完成跳转或取消。有多种方式可以在路由导航发生时执行钩子：全局的, 单个路由独享的, 或者组件级的。

你可以使用 router.beforeEach 注册一个全局的 before 钩子：

    const router = new VueRouter({ ... })
    
    router.beforeEach((to, from, next) => {
      // ...
    })

当一个导航触发时，全局的before钩子按照创建顺序调用。钩子是异步解析执行，此时导航在所有钩子 resolve 完之前一直处于 **等待中**。

每个钩子方法接收三个参数：

**to**: Route: 即将要进入的目标 路由对象

**from**: Route: 当前导航正要离开的路由

**next**: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

**next()**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed （确认的）。

**next(false)**: 中断当前的导航。如果浏览器的 URL 改变了（可能是用户手动或者浏览器后退按钮），那么 URL 地址会重置到 from 路由对应的地址。

**next('/')** 或者 **next({ path: '/' })**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。

**确保要调用 next 方法，否则钩子就不会被 resolved。**

同样可以注册一个全局的 after 钩子，不过它不像 before 钩子那样，after 钩子没有 next 方法，不能改变导航：

    router.afterEach(route => {
      // ...
    })

组件内的导航钩子

    const Foo = {
      template: `...`,
      beforeRouteEnter (to, from, next) {
        // 在渲染该组件的对应路由被 confirm 前调用
        // 不！能！获取组件实例 `this`
        // 因为当钩子执行前，组件实例还没被创建
      },
      beforeRouteUpdate (to, from, next) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
      },
      beforeRouteLeave (to, from, next) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
      }
    }

只有在组件穿件之后，当前组件实例this才可以使用

## 路由元信息

首先，我们称呼 routes 配置中的每个路由对象为 路由记录。

## 数据获取

当你使用导航完成后获取数据这种方式时，我们会马上导航和渲染组件，然后在组件的 created 钩子中获取数据。这让我们有机会在数据获取期间展示一个 loading 状态，还可以在不同视图间展示不同的 loading 状态。

## 路由配置

**routes**

类型: `Array<RouteConfig>`

RouteConfig 的类型定义：

    declare type RouteConfig = {
      path: string; //路径，必须
      component?: Component; // 组件
      name?: string; // for named routes (命名路由) 
      components?: { [name: string]: Component }; // for named views (命名视图组件)
      redirect?: string | Location | Function; //重定向
      alias?: string | Array<string>; //别名
      children?: Array<RouteConfig>; // for nested routes 
      beforeEnter?: (to: Route, from: Route, next: Function) => void;
      meta?: any; //元信息
    }

**mode**

类型: string

默认值: "hash" (浏览器环境) | "abstract" (Node.js 环境)

可选值: "hash" | "history" | "abstract"

配置路由模式:

**hash**: 使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器。

**history**: 依赖 HTML5 History API 和服务器配置。查看 HTML5 History 模式.

**abstract**: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式。

**base**

类型: string

默认值: "/"

应用的基路径。例如，如果整个单页应用服务在 /app/ 下，然后 base 就应该设为 "/app/"。

**linkActiveClass**

类型: string

默认值: "router-link-active"

全局配置 <router-link> 的默认『激活 class 类名』。参考 router-link.

**scrollBehavior**

类型: Function

签名:

    (
      to: Route,
      from: Route,
      savedPosition?: { x: number, y: number }
    ) => { x: number, y: number } | { selector: string } | ?{}