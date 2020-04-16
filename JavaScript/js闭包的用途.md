layout: '[layout]'
title: js闭包的用途
date:  2016-05-23 19:19:15
tags: [js]
categories: [js]
---
---js闭包的用途---
  
# 说明
 
闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。
怎么来理解这句话呢？请看下面的代码。
 
```js
　　function f1(){
　　　　var n=999;
　　　　nAdd=function(){n+=1}
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
　　nAdd();
　　result(); // 1000
```