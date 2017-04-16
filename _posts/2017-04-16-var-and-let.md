---
layout: post
title: var 与   let 的区别
date: 2017-04-16 22:44:00
categories: blog
tags: [js]
description: ES6中let与var的区别
---

## 前言

ES6出来这么久了，还没怎么看过，感觉自己不试一个合格的前端。捂脸。。。
感觉是时候研究一波ES6了。。。。。

听说好多大神都已经弃用var了，想想自己每天var声明变量乐此不疲的，不说了，说多了丢人。

>var 在一定程度上存在缺陷，其实在 strong mode 下使用 var 会直接报 SyntaxError

最简单的方式就是全局替换成 let，但建议注意一下 const。let 和 const 区别并不大，const 为常量，let 为变量。我的使用方式是只要定义的不会改变就用 const，然后你会发现大部分使用的是 const。

## let

第一次接触let关键字，有一个要非常非常要注意的概念就是”JavaScript 严格模式”，比如下述的代码运行就会报错：
	let hello = 'hello world.';
	console.log(hello)

解决方法： 在文件头添加”javascript 严格模式”声明：
	'use strict';
	let hello = 'hello world.';
	console.log(hello);



## 异同

第一种情况:声明后未赋值，表现相同
	'use strict';
	 (function() { 
	 	var varTest;
	 	let letTest; 
	 	console.log(varTest); //输出undefined 
	 	console.log(letTest); //输出undefined 
	 }());
 
 
第二种:使用未声明的变量，表现不同:
	(function() {
	  console.log(varTest); //输出undefined(注意要注释掉下面一行才能运行)
	  console.log(letTest); //直接报错：ReferenceError: letTest is not defined
	
	  var varTest = 'test var OK.';
	  let letTest = 'test let OK.';
	}());

第三种情况：重复声明同一个变量时，表现不同

	'use strict';
	
	(function() {
	  var varTest = 'test var OK.';
	  let letTest = 'test let OK.';
	  
	  var varTest = 'varTest changed.';
	  let letTest = 'letTest changed.'; //直接报错：SyntaxError: Identifier 'letTest' has already been declared
	
	  console.log(varTest); //输出varTest changed.(注意要注释掉上面letTest变量的重复声明才能运行)
	  console.log(letTest);
	}());


第四种情况：变量作用范围，表现不同

	'use strict';
	
	(function() {
	  var varTest = 'test var OK.';
	  let letTest = 'test let OK.';
	
	  {
	    var varTest = 'varTest changed.';
	    let letTest = 'letTest changed.';
	  }
	
	  console.log(varTest); //输出"varTest changed."，内部"{}"中声明的varTest变量覆盖外部的letTest声明
	  console.log(letTest); //输出"test let OK."，内部"{}"中声明的letTest和外部的letTest不是同一个变量
	}());




### 参考文章

[技术链接](http://blog.csdn.net/nfer_zhuang/article/details/48781671)

