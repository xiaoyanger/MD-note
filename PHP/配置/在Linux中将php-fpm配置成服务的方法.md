layout: '[layout]'
title: 在Linux中将php-fpm配置成服务的方法
date: 2015-11-12 00:00:00
tags: [linux,php]
---
在Linux中将php-fpm配置成服务的方法

# 配置php-fpm.conf

```sh
    #vi /usr/local/php/etc/php-fpm.conf
   php-fpm.pid 目录必须指向：/usr/local/php/var/run/php-fpm.pid
```

# 拷贝php-fpm脚本至/etc/init.d目录

```sh
    #cp /home/soft/php-5.3.15/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
```

# 设置权限并启动php-fpm：

```sh
    #chmod 755 /etc/init.d/php-fpm
    #/etc/init.d/php-fpm start
    #chkconfig –add php-fpm
```

# 最后，给出php-fpm以服务的方式启动、停止和重启：

```sh
    #service php-fpm start
    #service php-fpm stop
    #service php-fpm reload
```

