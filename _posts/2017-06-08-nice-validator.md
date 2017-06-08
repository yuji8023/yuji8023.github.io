---
layout: post
title: nice-validator 的使用(一)
date: 2017-06-11 08:27:00
categories: blog
tags: [js]
description: nice-validator 的使用(一)
---


> [nice-validator](https://validator.niceue.com/) 依赖 jQuery(1.7+)。除了直接引用插件，还支持 AMD 模块系统。


## 前言

> 一个月前在逛某乎的时候被人安利了这个表单验证插件，最近一个月新做一个项目，不在局限于老项目上的技术栈。刚好我也厌倦了之前使用的那个验证插件，抱着学习的心态就尝试着使用了一下。感觉还不错，就现有的茫茫多验证插件里，我觉得就冲官网的简洁，也值得一用了。

## 使用

使用的方法很简单。直接一行代码引入就好。

~~~
<script src="path/to/nice-validator/jquery.validator.js?local=zh-CN"></script>
~~~

local 参数用来加载对应的配置文件。如果不传 local 参数，配置以及样式就需要自行引入。

还有一种是通过RequireJS模块系统引入。

~~~
requirejs.config({
    paths: {
        jquery: 'http://cdn.jsdelivr.net/jquery/1.12.3/jquery.min',
        validator: 'path/to/nice-validator/local/zh-CN'
    },
    shim: {
        validator: ['path/to/nice-validator/jquery.validator.js?css']
    }
});
require(['jquery', 'validator'], function($){
    $('#form1').validator();
});
~~~

## 内置规则

nice-validator 内置 8 个规则，包括：

* required - 使字段必填
* checked - 必选，还可以控制选择项目的数量
* match - 当前字段与另一个字段比较
* remote - 获取服务器端验证的结果
* integer - 只能填写整数
* range - 只能填写指定范围的数
* length - 字段值必须符合指定长度
* filter - 过滤当前字段的值，不做验证

具体使用方法在这里不多做介绍，感兴趣的人可以去官网去看文档与源码。

* [Github传送门](https://github.com/niceue/nice-validator);
* [官网传送门](https://validator.niceue.com/);

## 使用经验

### 初始化验证

~~~
$("#newAdminForm").validator({
	formClass:"n-default",
	msgStyle:"margin-top:8px;",
	rules:{
		identityId:[/^\d{6}(19|2\d)?\d{2}(0[1-9]|1[012])(0[1-9]|[12]\d|3[01])\d{3}(\d|X)?$/, "格式不对"],
    	mobile: [/^1[3-9]\d{9}$/, "格式不对"]
    },
    fields: {
    	realName:{
    		rule:"required;"
    	},
    }，
    messages: {
        required: "必填",
    },
    showOk:" ",
    invalidClass :"error-box",
});
~~~

*注*：在配置文件`zh-CN.js`里有很多写好的验证规则，可以直接使用。

### 验证失败事件

在表单验证不通过后触发
~~~
$('#newAdminForm').on('invalid.form', function(e, result){
	//在这里验证失败的时候会将焦点放在第一个失败的表单上
	$(".error-box:first").focus();
});
~~~

### 验证成功事件

在表单验证通过后触发
~~~
$("#newAdminForm").on("valid.form",function(e,form){
	console.log("验证通过");
	var data = $("#newAdminForm").serialize();
	saveModelAdmin(data);//验证通过后调用表单提交方法
})
~~~

### 触发表单验证

点击保存按钮触发表单验证
~~~
$("#saveNewBasManY").on("click",function(){
	$("#newAdminForm").trigger("validate");
})
~~~

### 销毁表单验证示例

有的时候我们需要多次为同一个表单添加验证多次，这个时候我们需要将原本的表单验证示例销毁后进行添加。

$('#newAdminForm').validator('destroy');

## 小结

我暂时也就用到这么多吧，基本也是够用了，遇到新的难题的时候应该多去看看官方文档与示例。就会有所启发。