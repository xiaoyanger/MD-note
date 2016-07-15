layout: '[layout]'
title: mysql权限分配
date: 2015-12-11 00:00:00
tags: [mysql]
---
授权并创建用户【命令解析-对号入座】

    grant select,insert,update,delete on laravel.* to admin@"%" identified by "admin123"
        权限
            select,insert,update,delete [全部权限时是：All]
        数据库.数据表
            laravel.*
        用户名@'登录方式'
            admin@"%"
        密码     
            admin23
撤销权限

    REVOKE privilege ON databasename.tablename FROM 'username'@'host';
删除用户

    DROP USER 'username'@'host';
更改用户密码

    SET PASSWORD FOR 'pig'@'%' = PASSWORD("123456");
刷新权限

    flush privileges;
