---
layout: post
title: vue-resourcce详解
date: 2017-09-06 00:11:00
categories: blog
tags: [vue]
description: The HTTP client for Vue.js
---

## 前言

vue-resource是什么？
在它的github上，是这么说的
`The plugin for Vue.js provides services for making web requests and handle responses using a XMLHttpRequest or JSONP.`

特性：

* 体积小Compact size 14KB (5.3KB gzipped)
* Supports the Promise API and URI Templates（Promise是ES6的特性，Promise的中文含义为“先知”，Promise对象用于异步计算。URI Templates表示URI模板，有些类似于ASP.NET MVC的路由模板。）
* Supports interceptors for request and response(拦截器是全局的，拦截器可以在请求发送前和发送请求后做一些处理。
拦截器在一些场景下会非常有用，比如请求发送前在headers中设置access_token，或者在请求失败时，提供共通的处理方式。)
* 支持主流浏览器（和Vue.js一样，vue-resource除了不支持IE 9以下的浏览器，其他主流的浏览器都支持。）

## 安装使用
  
npm安装

    $ npm install vue-resource  --save

在项目中引入

    import VueResource from 'vue-resource'
    Vue.use(VueResource);

use

    // 基于全局Vue对象使用http
    Vue.http.get('/someUrl', [options]).then(successCallback, errorCallback);
    Vue.http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);
    
    // 在一个Vue实例内使用$http
    this.$http.get('/someUrl', [options]).then(successCallback, errorCallback);
    this.$http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);

simple example

    {
      // GET /someUrl
      this.$http.get('/someUrl').then(response => {
        // get body data
        this.someData = response.body;
      }, response => {
        // error callback
      });
    }

一般用法

    this.$http({
        url: '',
        method: 'POST',
        // 请求体重发送的数据
        data: {
            cat: 1
        },
        // 设置请求头
        headers: {
            'Content-Type': 'x-www-from-urlencoded'
        }
    }).then(function () {
        // 请求成功回调
    }, function () {
        // 请求失败回调
    });

## Configuration

如果Web服务器无法处理PUT, PATCH和DELETE这种REST风格的请求，你可以启用enulateHTTP现象。启用该选项后，请求会以普通的POST方法发出，并且HTTP头信息的`X-HTTP-Method-Override`属性会设置为实际的HTTP方法。

    Vue.http.options.emulateHTTP = true;

`emulateJSON`的作用
如果Web服务器无法处理编码为`application/json`的请求，你可以启用emulateJSON选项。启用该选项后，请求会以`application/x-www-form-urlencoded`作为MIME type，就像普通的HTML表单一样。

    Vue.http.options.emulateJSON = true;

## response对象

response对象包含以下属性：

    方法     类型    描述
    text()  string  以string形式返回response body
    json()  Object  以JSON对象形式返回response body
    blob()  Blob    以二进制形式返回response body
    属性        类型     描述
    ok          boolean 响应的HTTP状态码在200~299之间时，该属性为true
    status      number  响应的HTTP状态码
    statusText  string  响应的状态文本
    headers     Object  响应头


## JSONP请求

**为了减少作用域链的搜索，建议使用一个局部变量来接收this。**

    getCustomers: function() {
      var vm = this
        vm.$http.jsonp(this.apiUrl).then(function(response){
            vm.$set('gridData', response.data)
        })
    }

## Resource

vue-resource提供了另外一种方式访问HTTP——resource服务，resource服务包含以下几种默认的action：

    get: {method: 'GET'},
    save: {method: 'POST'},
    query: {method: 'GET'},
    update: {method: 'PUT'},
    remove: {method: 'DELETE'},
    delete: {method: 'DELETE'}

resource对象也有两种访问方式：

全局访问：Vue.resource
实例访问：this.$resource
resource可以结合URI Template一起使用，以下示例的apiUrl都设置为{/id}了：

apiUrl: 'http://211.149.193.19:8080/api/customers{/id}'

官方给出的两个例子比较有意思：

#### Example

    {
      var resource = this.$resource('someItem{/id}');
      
      // GET someItem/1
      resource.get({id: 1}).then(response => {
        this.item = response.body;
      });
    
      // POST someItem/1
      resource.save({id: 1}, {item: this.item}).then(response => {
        // success callback
      }, response => {
        // error callback
      });
    
      // DELETE someItem/1
      resource.delete({id: 1}).then(response => {
        // success callback
      }, response => {
        // error callback
      });
    }

延伸出第二个例子

#### Custom Actions

    {
      var customActions = {
        foo: {method: 'GET', url: 'someItem/foo{/id}'},
        bar: {method: 'POST', url: 'someItem/bar{/id}'}
      }
    
      var resource = this.$resource('someItem{/id}', {}, customActions);
    
      // GET someItem/foo/1
      resource.foo({id: 1}).then(response => {
        this.item = response.body;
      });
    
      // POST someItem/bar/1
      resource.bar({id: 1}, {item: this.item}).then(response => {
        // success callback
      }, response => {
        // error callback
      });
    }

仔细思考的话，这种方式的写法可以将自定义的actions单独放在一个文件里面，比较方便统一管理

## 使用inteceptor

拦截器可以在请求发送前和发送请求后做一些处理。
<img style="background-image: none; padding-top: 0px; padding-left: 0px; display: inline; padding-right: 0px; border-width: 0px;" title="image" src="http://images2015.cnblogs.com/blog/341820/201607/341820-20160710080109342-1842115136.png" alt="image" width="514" height="479" border="0">

example

    Vue.http.interceptors.push((request, next) => {
        help.showLoading = true
        next((response) => {
            if(!response.ok){
                help.errorCode = response.status
                help.showDialog = true
            }
            help.showLoading = false
            return response
        });
    });

基本用法

    Vue.http.interceptors.push((request, next) => {
            // ...
            // 请求发送前的处理逻辑
            // ...
        next((response) => {
            // ...
            // 请求发送后的处理逻辑
            // ...
            // 根据请求的状态，response参数会返回给successCallback或errorCallback
            return response
        })
    })

## 总结

vue-resource是一个非常轻量的用于处理HTTP请求的插件，它提供了两种方式来处理HTTP请求：

* 使用Vue.http或this.$http
* 使用Vue.resource或this.$resource
* 
这两种方式本质上没有什么区别，阅读vue-resource的源码，你可以发现第2种方式是基于第1种方式实现的。

inteceptor可以在请求前和请求后附加一些行为，这意味着除了请求处理的过程，请求的其他环节都可以由我们来控制。

## 参考

* [vue-resource](https://github.com/pagekit/vue-resource)
* [一起玩转vue-resource](http://www.jianshu.com/p/3ce2bd36596e)
* [Vue.js——vue-resource全攻略](http://www.cnblogs.com/keepfool/p/5657065.html)
* [Vue_VueResource](https://segmentfault.com/a/1190000007087934)