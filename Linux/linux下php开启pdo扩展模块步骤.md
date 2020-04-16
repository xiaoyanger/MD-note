layout: '[layout]'
title: linux下php开启pdo扩展模块步骤
date: 2016-1-7 00:00:00
tags: [linux,mysql,pdo]
---
# linux下php开启pdo扩展模块步骤

假设php目录为/usr/local/php
mysql目录为/usr/local/mysql

## 进入PHP源码包ext/pdo目录

```sh
    #cd ext/pdo
```

## 执行/usr/local/php/bin/phpize[假设PHP的安装目录为/usr/local/php]

```sh
    #/usr/local/php/bin/phpize
```
    
## 配置扩展pdo

```sh
    #./configure --with-php-config=/usr/local/php/bin/php-config --enable-pdo=shared
```

## 编译 && 安装pdo

```sh
    #make && make install
```

   成功则出现

```
        Installing shared extensions: /usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/
        Installing header files: /usr/local/php//include/php/
        Installing PDO headers: /usr/local/php//include/php/ext/pdo/
```

   说明在/usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/目录下生成了pdo.so文件
## 在PHP源码包中进入pdo_mysql

```sh
    #cd ext/pdo_mysql
```

## /usr/local/php/bin/phpize

```sh
    #/usr/local/php/bin/phpize
```

## 配置pdo_mysql[假设Mysql的安装目录为/usr/local/mysql]

```sh
    #./configure --with-php-config=/usr/local/php/bin/php-config --with-pdo-mysql=/usr/local/mysql
```

## 编译安装pdo_mysql

```sh
    #make && make install
```

   成功则出现

```
    Installing shared extensions: /usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/
``

   说明的/usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/目录下生成了pdo_mysql.so文件
## 在php配置文件中加上

```
    extension="/usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/pdo_mysql.so";
    extension="/usr/local/php//lib/php/extensions/no-debug-non-zts-20060613/pdo.so";
```

## 重启相关服务

    #/etc/init.d/php-fpm restart
    
# END
