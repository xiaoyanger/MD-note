layout: '[layout]'
title: linux，apache，php，mysql常用的查看版本信息的方法
date: 2015-11-3 00:00:00
tags: [layout,mysql,php,apache,linux]
---
linux，apache，php，mysql常用的查看版本信息的方法

## 查看linux的内核版本，系统信息，常用的有三种办法：
    
    uname -a;//显示电脑以及操作系统的相关信息
    more /etc/issue;//显示的是发行版本信息
    cat /proc/version;//显示正在运行的内核版本

## 查看apache的版本信息:
    
    如果是通过yum，或者是rpm安装的，可以使用rpm -qa |gerp httpd 来查看；
    还可以通过httpd -v来查询；
    当然，安装好apache后，可以直接elink回环查看apache的信息。
    
## 查看php的版本信息:

    如果是通过yum，或者是rpm包安装的，可以使用rpm -qa |grep php来查看；
    同样，也可以使用php -v来查看php的版本信息；
    一般情况下，大多是通过安装lamp后，使用phpinfo的测试页来查看安装的php的信息；
    
## 查看mysql的版本信息，如果是通过yum安装的，或者是rpm包安装的，可以使用rpm -qa |grep mysql 来查看；
    
    也可以使用mysql -v 或者是--help|grep Distrib来查看；
    也可以进入mysql，然后通过命令select version（）；来查看；
    或者是status；命令查看。