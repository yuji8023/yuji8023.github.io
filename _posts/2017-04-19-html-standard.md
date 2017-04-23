---
layout: post
title: html规范文档
date: 2017-04-19 23:04:00
categories: blog
tags: [html]
description: html规范
---

## 前言

公司最近让整理一份前端的技术文档，前端主管就给我安排了个编写文档的工作。<br>
主管写了其中大部分，然后我又查了一些各方面的html规范，略作补充。以后会补充的更加完善的。。。

## 基本规范

* 使用<!Doctype html> 文档类型声明，h5的最新声明方式 `<!DOCTYPE html> `
* 设置网页的编码以及文档类型 <br>

	`<meta http-equiv ="Content-Type" content ="text/html; charset=utf-8" />`

* 设置网页的渲染模式，按照最新的模式渲染
	`<meta http-equiv ="X-UA-Compatible" content ="IE=edge,chrome=1">`
* 如果是移动页面或者使用boot框架，设置meta的读取情况。
	`<meta name="viewport" content="width=device-width; initial-scale=1.0;maximum-scale=1.0; user-scalable=no;"/>`
* 网站的相关meta属性需要设置的设置，比如网站关键字，作者，描述，网站图标，请本站搜索meta关键字
* js以及css相关文档在head中引入，其中type属性可以省略，默认正确的，link标签的rel属性不可省略。

    `<script src = "js/jquery-1.11.1.min.js" ></script>`
	`<link rel = "stylesheet" href = "css/bootstrap.min.css" >`

* 针对ie引入的文件写入注释语句中，注释条件语法如下，现在只针对ie8以上版本适配，需要的适配文件见有关资源。目前已经收集的兼容文件有兼容h5,媒体查询响应，css3选择器，css3部分属性。（备注：css3样式生效的前提是样式写在外链的样式文件中，写在style中是无效的）。


~~~ html
   <!--[if  lt IE 9]>
   <script src="//cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
   <script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
   <script src="../js/selectivizr.js"></script>
   <noscript><link rel="stylesheet" href="[fallback css]" /></noscript>
   <script src="../js/PIE.js"></script>
  <![endif]-->
   # pie 的需要追加绑定脚本
   $(function() {
	  if (window.PIE) {
	    $( '.container-fluid' ).each(function () {
		  PIE.attach(this);
		});
	  }
  });
~~~
 

   
> 将html5shiv复制到head部分，记住一定要是head部分（因为IE必须在元素解析前知道这个元素，所以这个js文件不能在其他位置调用，否则失效）<br>
> 最后在css里面加上这段：

	/*html5*/
	article,aside,dialog,footer,header,section,footer,nav,figure,menu{display:block}

> 主要是让这些html5标签成块状，像div那样。<br>
> 一句话概括就是：引用html5.js使html5标签成块状。<br>
> 而Respond.js让不支持css3 Media Query的浏览器包括IE6-IE8等其他浏览器支持查询。<br>
> 引入respond.min.js，但要在css的后面（越早引入越好，在ie下面看到页面闪屏的概率就越低，因为最初css会先渲染出来，如果respond.js加载得很后面，这时重新根据media query解析出来的css会再改变一次页面的布局等，所以看起来有闪屏的现象）


* 针对jquery框架，特殊说明，pc网站的统一使用jq1.12.1版本，以便可以兼容ie678，手机端使用zepto最新版本即可（1.2.0），并且在其他js引入顺序之前。
* 设置head内title标签属性，设置正确的网页标题。
* 页面标签内不写脚本以及样式文件，实现网页的结构、样式、行为三层分离，也避免写style标签样式，避免focu现象。
* 与dom有关的脚本绑定事件，写在domready之后，即可以加快页面的载入速度，也能避免事件绑定失败。特别注意的是，如果是自定义非绑定事件，写在domready外面。
* 页面文件，img，css等文件的命名使用英文语义化，使用驼峰式命名，对于特殊含义的使用中划线分割，对于格式以及访问层级的使用.,禁止使用中文，不建议使用拼音。备注：文件名称不宜过长控制在4个单词以内。
* 关于css样式的class用‘-’连接，用于DOM操作的class用驼峰命名，这样可以在修改样式的时候不会导致js失效。修改js的时候不会导致样式错乱。
* 待补充...


## 语法规范

* 标签以及标签属性小写
* 可省略的闭合标签不省略，自闭合的标签可不写结束斜线。eg:`<br>`
* 嵌套的标签必须被正确的嵌套，嵌套的子元素有一格的缩进
* 使用语义化的标签，例如`header,footer,nav,article,filedset,section`等，避免全页使用div布局。
* 针对img标签，写有合适的`alt`以及`title`属性，用于描述图片信息。避免出现src="" 问题
* 对于标题项或者图片等，如果有需要显示的交互内容，建议写title属性，用于提示内容，在新浪以及淘宝等网站的图片以及标题中都有title属性，用于增加页面友好度，需要注意的是如果页面元素本身有交互效果或者弹框，可以省去。
* 页面有完整的html结构，包括html,head,body标签。
* 尽可能精简页面结构，标签最大可能性的是用于存放内容的，如果需要特殊样式用css实现，减少无意义的空标签。这个称作页面重构。
* 格式化规则：每一个块级元素，新增空白行，行级元素写在同一行，同时子元素有两个空格的缩进。需要注意的是，行级元素换行会引起不必要的空白外间距，避免方式有两种，一种就是行级元素写在同一行，另外一种就是父元素设置`font-size:0`，当然也可以取巧，只把行级元素的开始标签头部写在上一行即可。
* html属性值一律建议使用双引号，格式化html的同时，也便于js编写dom结构。（js为单引号）
* 属性的使用顺序` class id name data- src for type href title alt aria role`
* 布尔型属性 不用赋值，有就是true，没有就是false
* 有良好的注释规范，针对页面模块有简要的注释，便于维护。


		单行注释  <!-- -->
		多行注释 
		<!--
		@name: 多行注释（模块名称）
		@description: add annotation doc（模块描述）
		@author:yuji（模块作者）
		-->

* 待补充...

###   友情链接
* [w3school官网-html](http://www.w3school.com.cn/html5/index.asp)
* [w3chtml教程](http://www.w3chtml.com/html5/course/)
* [极客学院html视频教程](http://search.jikexueyuan.com/course/?q=HTML5)
* [前端规范](http://front-end-standards.com/#24js)

