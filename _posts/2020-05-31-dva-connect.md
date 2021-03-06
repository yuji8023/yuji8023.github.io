---
layout: post
title: dva 的 connect 与 @connect
date: 2020-05-31 21:12:00
categories: blog
tags: [dva]
description: dva 的 connect 与 @connect
---

### connect 的使用

> connect 方法返回的也是一个 React 组件，通常称为容器组件。因为它是原始 UI 组件的容器，即在外面包了一层 State。connect 方法传入的第一个参数是 mapStateToProps 函数，该函数需要返回一个对象，用于建立 State 到 Props 的映射关系。

简而言之，connect接收一个函数，返回一个函数。

第一个函数会注入全部的models，你需要返回一个新的对象，挑选该组件所需要的models。

```javascript
export default connect(({ user, login, global = {}, loading }) => ({
    currentUser: user.currentUser,
    collapsed: global.collapsed,
    fetchingNotices: loading.effects['global/fetchNotices'],
    notices: global.notices
}))(BasicLayout);


// 简化版
export default connect( 
  ({ user, login, global = {}, loading }) => {
        return {
          currentUser: user.currentUser,
          collapsed: global.collapsed,
          fetchingNotices: loading.effects['global/fetchNotices'],
          notices: global.notices
        }
  }
)(BasicLayout);
```

@connect 是connect的装饰器、语法糖罢了。

### connect 的四个参数

> connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])

