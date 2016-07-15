layout: '[layout]'
title: 修改php.ini报错级别
date: 2016-02-25 13:30:09
tags:  [php]
---
修改php.ini报错级别

~~~~~~~~~~~~~~~
error_reporting = E_ALL & ~E_NOTICE

;有关error_reporting()函数：

error_reporting() 设置 PHP 的报错级别并返回当前级别。

; 错误报告是按位的。或者将数字加起来得到想要的错误报告等级。
; E_ALL - 所有的错误和警告
; E_ERROR - 致命性运行时错
; E_WARNING - 运行时警告（非致命性错）
; E_PARSE - 编译时解析错误
; E_NOTICE - 运行时提醒(这些经常是是你的代码的bug引起的，

;也可能是有意的行为造成的。(如：基于未初始化的变量自动初始化为一个
;空字符串的事实而使用一个未初始化的变量)

; E_CORE_ERROR - 发生于PHP启动时初始化过程中的致命错误
; E_CORE_WARNING - 发生于PHP启动时初始化过程中的警告(非致命性错)
; E_COMPILE_ERROR - 编译时致命性错
; E_COMPILE_WARNING - 编译时警告(非致命性错)
; E_USER_ERROR - 用户产生的出错消息
; E_USER_WARNING - 用户产生的警告消息
; E_USER_NOTICE - 用户产生的提醒消息

;使用方法：

error_reporting(0);//禁用错误报告
error_reporting(E_ALL ^ E_NOTICE);//显示除去 E_NOTICE 之外的所有错误信息
error_reporting(E_ALL^E_WARNING^E_NOTICE);//显示除去E_WARNING E_NOTICE 之外的所有错误信息
error_reporting(E_ERROR | E_WARNING | E_PARSE);//显示运行时错误，与error_reporting(E_ALL ^ E_NOTICE);效果相同。error_reporting(E_ALL);//显示所有错误

~~~~~~~~~~~~~~~