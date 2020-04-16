layout: '[layout]'
title: js刷新当前页面的几种方法
date:  2016-05-08 13:16:55
tags: [js]
categories: [js]
---
# js刷新当前页面的几种方法
 
 ```js
history.go( 0)
location.reload()
location=location
location.assign(location)
document.execCommand( 'Refresh')
window.navigate(location)
location.replace(location)
document.URL=location.href
```