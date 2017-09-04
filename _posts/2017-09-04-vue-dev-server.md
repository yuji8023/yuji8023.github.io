---
layout: post
title: vue项目的proxy代理设置dev-server
date: 2017-09-05 00:54:00
categories: blog
tags: [vue]
description: vue项目的proxy代理设置dev-server
---

## 前言

在vue的开发中，由于前后端分离后，服务端和前端的开发环境处于2台不同的机器上，整个联调过程，入口页面需要引用前端机器的静态资源，又要调用后端机器的异步接口。

## proxy代理

通过在本地 dev-server 中使用 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 中间件把接口请求代理到后端机器，vue-cli 生成的 dev-server 中已经自带了这个功能：

    // proxy api requests
    Object.keys(proxyTable).forEach(function (context) {
    var options = proxyTable[context]
    if (typeof options === 'string') {
      options = { target: options }
    }
    app.use(proxyMiddleware(context, options))
  });
最好通过启动 `dev-server` 时传入一个参数来控制是否打开代理功能，这样可以避免开发阶段覆盖掉我们的 `mock` 配置。

## dev-server原理

在上面的`proxy api requests`的代码里里遍历你proxyTable中的所有配置项、并且用appuse开启proxyMiddleware这个代理中间件。

相当于你的请求是在请求dev-server、然后node在请求你的远端服务器、后台之间请求就没有跨域问题了、请求到了、再返回给你。

我们使用browserSync之类的调试工具、也是要用node做中间层代理的、这样前后端分离、不用打包成一个项目、还可以互相请求。

所以你这里network返回的应该是本地的url、也就是你请求node的信息。

更多原理可以到[http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware)官方网站去看。

## 如何设置接口代理

一、首先找到`config`文件下的`index.js`.
二、找到dev里面的`proxyTable`他的值就是一个{}，这里为了方便配置配置文件单独写成一个文件(__推荐写法__)

    dev: {
      env: require('./dev.env'),
      port: 8080,
      autoOpenBrowser: true,
      assetsSubDirectory: 'static',
      assetsPublicPath: '/',
      proxyTable: proxyConfig.proxyList,
      cssSourceMap: false
    }

三、配置`proxyConfig`文件

    module.exports = {
      proxyList: {
        '/api':{
            target: 'http://10.10.10.10：8080',//接口域名
            changeOrigin: true,  //是否跨域
            pathRewrite: {
              '^/api': ''  //需要rewrite重写的,
            }
        }
      }
    }

四、访问`localhost:8080/api`就相当于访问下面地址

    http://10.10.10.10：8080

这样用代理就解决了开发中跨域的问题，当然可以直接proxyTable后面写（但是 __不建议__） 

## 动态启动代理

最好通过启动 dev-server 时传入一个参数来控制是否打开代理功能，这样可以避免开发阶段覆盖掉我们的 mock 配置。

方法：

在dev-server.js里将

    // proxy api requests
    Object.keys(proxyTable).forEach(function (context) {
      var options = proxyTable[context]
      if (typeof options === 'string') {
        options = { target: options }
      }
      app.use(proxyMiddleware(context, options))
    });

替换为

    // mock/proxy api requests
    var mockDir = path.resolve(__dirname, '../mock');
    (function setMock(mockDir) {
      fs.readdirSync(mockDir).forEach(function (file) {
        var filePath = path.resolve(mockDir, file);
        var mock;
        if (fs.statSync(filePath).isDirectory()) {
          setMock(filePath);
        }
        else {
          mock = require(filePath);
          app.use(mock.api, argv.proxy ? proxyMiddleware({target: 'http://' + argv.proxy}) : mock.response);
        }
      });
    })(mockDir);;

当然，想要使用本地mock功能，一般需要在本地用express或者其他方式搭建本地服务器

参考链接[部署方案](https://www.zhihu.com/question/38213423/answer/128155176)

## 扩展

* [vue-cli脚手架config目录下index.js配置文件详解](http://www.cnblogs.com/ye-hcj/p/7077796.html)
* [dev-server.js浅析](http://www.cnblogs.com/moneyss/p/7098445.html)
* [webpack开发和生产两个环境的配置详解](http://blog.csdn.net/itkingone/article/details/70331783)
* vue-cli使用的模板插件[API proxy](https://vuejs-templates.github.io/webpack/proxy.html)