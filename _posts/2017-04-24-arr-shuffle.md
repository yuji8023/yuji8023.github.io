---
layout: post
title: js算法
date: 2017-04-26 21:23:00
categories: blog
tags: [js]
description: js的乱序算法与正序算法
---

## 乱序

在网上看到了一个效率较高的一个乱序算法

~~~
if (!Array.prototype.shuffle) {
    Array.prototype.shuffle = function() {
        for(var j, x, i = this.length; i; j = parseInt(Math.random() * i), x = this[--i], this[i] = this[j], this[j] = x);
        return this;
    };
}
~~~

这个算法会遍历一次，每次都会在前面随机一个数两两交换位置。

不过这个写法还是比较有意思的，直接是在for循环的条件里完成了逻辑运算，没有`{}`，算是开拓了眼界。

4月30日补充：

> for循环的三个部分（初始化，循环条件，自增操作）都可以写成用逗号分隔的多重表达式，循环体的内容也可以移到自增部分中去。
——《JavaScript面向对象编程指南》


## 正序

~~~
 var times=0;
 var quickSort=function(arr){
     //如果数组长度小于等于1无需判断直接返回即可
     if(arr.length<=1){
         return arr;
     }
     var midIndex=Math.floor(arr.length/2);//取基准点
     var midIndexVal=arr.splice(midIndex,1);//取基准点的值,splice(index,1)函数可以返回数组中被删除的那个数arr[index+1]
     var left=[];//存放比基准点小的数组
     var right=[];//存放比基准点大的数组
     //遍历数组，进行判断分配
     for(var i=0;i<arr.length;i++){
         if(arr[i]<midIndexVal){
             left.push(arr[i]);//比基准点小的放在左边数组
         }
         else{
             right.push(arr[i]);//比基准点大的放在右边数组
         }
         console.log("第"+(++times)+"次排序后："+arr);
     }
     //递归执行以上操作,对左右两个数组进行操作，直到数组长度为<=1；
     return quickSort(left).concat(midIndexVal,quickSort(right));
 };
 console.log(quickSort(arr));
~~~

这个算法是每次娶一个中间值，然后循环遍历数组中的其他值与这个中间值进行比较，小的放在左边，大的放在右边。

假如有一个长度为10的数组，像这样只需要循环遍历22次就可以完成排序。



