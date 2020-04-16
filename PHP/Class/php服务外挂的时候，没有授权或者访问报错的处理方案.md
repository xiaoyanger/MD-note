layout: '[layout]'
title: php服务外挂的时候，没有授权或者访问报错的处理方案
date: 2018年12月27日13:16:40
tags: [nginx,php,php-fpm]
categories: [nginx,php,php-fpm]
---

### php服务外挂的时候，没有授权或者访问报错的处理方案

报错如下

```
An error occurred.
Sorry, the page you are looking for is currently unavailable.
Please try again later.

If you are the system administrator of this resource then you should check the error log for details.

Faithfully yours, nginx.
```


解决方法
1. nginx配置文件进行调整，增加用户和用户组设定
    ```
         user   www;
         
    ```
2. php-fpm.conf 文件修改配置项
    ```
        、、修改监听端口，不只是监听        127.0.0.1
        listen = [::]:9000
        允许客户端的访问ip限制关闭
        listen.allowed_clients = 127.0.0.1

    ```