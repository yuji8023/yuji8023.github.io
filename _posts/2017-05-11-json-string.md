---
layout: post
title: parse()与stringify()
date: 2017-05-11 00:27:00
categories: blog
tags: [json]
description: JSON.parse()与JSON.stringify()
---


## JSON.parse()

用于从一个字符串中解析出json 对象。
~~~
var str='{"name":"yuji","age":"23"}';
~~~
经 JSON.parse(str) 得到：
~~~
Object: age:"23"
        name:"yuji"
        _proto_:Object
~~~
> 注意：单引号写在{}外，每个属性都必须双引号，否则会抛出异常


## JSON.stringify()

用于从一个对象解析出字符串。
~~~
var a={a:1,b:2}
~~~
经 `JSON.stringify(a)`得到：
~~~
"{"a":1,"b":2}"
~~~
