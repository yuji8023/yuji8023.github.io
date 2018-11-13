---
layout: post
title: Express 常用中间件
date: 2018-11-13 10:30:00
categories: blog
tags: [Express]
description: Express常用的中间件介绍及用法
---

## static

静态资源文件

```javascript
  express.static(root, [options])
```
参数

```javascript
var options = {
  dotfiles: 'ignore',
  etag: false,
  extensions: ['htm', 'html'],
  index: false,
  maxAge: '1d',
  redirect: false,
  setHeaders: function (res, path, stat) {
    res.set('x-timestamp', Date.now());
  }
}

app.use(express.static('public', options));
```
```javascript
app.use(express.static(path.join(__dirname, 'public')));
```
这行代码将HelloExpress目录下的public目录作为静态文件交给static中间件来处理，对应的HTTP URI为“/”。path是一个Node.js模块，__dirname是Node.js的全局变量，指向当前运行的js脚本所在的目录。path.join()则用来拼接目录。

## Router

一个对象，行为很像中间件，但是又可以向中间件那样使用router，也可以添加中间件。

创建Router实例

```javascript
var router = express.Router([options]);
```
定义了router后，也可以将其作为中间件传递给app.use：

```javascript
  var routes = require('./routes/index');
  ...
  app.use('/', routes);
```
[blog](https://blog.csdn.net/foruok/article/details/47354737)

## body-parser 

一个HTTP请求体解析中间件。

下载

```javascript
    npm install body-parser --save
```
使用

```javascript
    var express = require('express');
    var bodyParser = require("body-parser")
    var app = express();

    // parse application/x-www-form-urlencoded
    app.use(bodyParser.urlencoded({ extended: false })) 
    // parse application/json
    app.use(bodyParser.json())  
```

[详细介绍]（https://www.jianshu.com/p/ea0122ad1ac0）

## express-session

一个处理Session的中间件。

下载
```javascript
    npm install express-session --save
```
使用
```javascript
var express = require('express');
var session = require('express-session');
var app = express();

// Use the session middleware 
app.use(session({ 
////这里的name值得是cookie的name，默认cookie的name是：connect.sid
  //name: 'hhw',
  secret: 'keyboard cat', 
  cookie: ('name', 'value', { path: '/', httpOnly: true,secure: false, maxAge:  60000 }),
  //重新保存：强制会话保存即使是未修改的。默认为true但是得写上
  resave: true, 
  //强制“未初始化”的会话保存到存储。 
  saveUninitialized: true,  
  
}))
```

[详细介绍]（https://www.jianshu.com/p/5a0ccd1ee27e）

## morgan

日志记录

无需安装，直接用用
```javascript
    var logger = require('morgan');
    app.use(logger('dev'));
```

[官方文档](https://www.npmjs.com/package/morgan)