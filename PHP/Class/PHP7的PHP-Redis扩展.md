layout: '[layout]'
title: PHP7的PHP-Redis扩展
date: 2016-1-9 00:00:00
tags: [linux,php,php7,Redis]
---

PHP7的PHP-Redis扩展

扩展下载地址：【百度云已经存放，可直接百度云下载】

<a href="https://codeload.github.com/edtechd/phpredis/zip/php7 ">https://codeload.github.com/edtechd/phpredis/zip/php7 </a>
    
# 解压下载包，执行下列命令

```sh
    #cd phpredis-php7/
    #/usr/local/php/bin/phpize
    #./configure --with-php-config=/usr/local/php/bin/php-config
    #make
    #make install
    #ls /usr/local/php/lib/php/extensions/
    #vim /usr/local/php/lib/php.ini
    #/usr/local/nginx/sbin/nginx -t#检测nginx
    #/usr/local/nginx/sbin/nginx -s reload#重启nginx
    #php -m // 出现redis扩展即可
```

# 测试php-redis的php文件
  
```php  
    <?php
        $redis = new Redis();
        $redis->connect('127.0.0.1',6379);
        $redis->set('Jay13','www.jb51.net');
        echo 'Jay13:'.$redis->get('Jay13');
        echo '</br>';
        echo 'Jay12:'.$redis->get('Jay12');
    ?>
```
- redis服务配置文件位置：redis-server /etc/redis.conf

# 重启php进程

```sh

    #killall php-fpm 
    #/usr/local/php/sbin/php-fpm
```