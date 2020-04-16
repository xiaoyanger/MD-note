layout: '[layout]'
title: nginx虚拟主机的配置文件
date: 2015-11-10 00:00:00
tags:  [nginx,linux]
---

在nginx的配置文件里引入nginx虚拟主机的配置文件【也可以直接写在nginx.conf(不推荐)】
下边是一个写好的配置文件，作为例子引用

```sh
    server {
         listen 80;
         server_name www.test.cn;
         root /home/test/;
         index index.php index.html index.htm;
         access_log /Data/logs/nginx/doctorcms access;
         location / {
            if (!-e $request_filename) {
                  rewrite ^/(.*)$ /index.php/$1 last;
            }
         }
         location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
         {
                expires      3d;
                access_log off;
         }
         location ~ .*\.(js|css)?$
         {
                expires      1d;
                access_log off;
          }
         location ~ \.git/ {
                deny all;
         }
         location ~ \.svn/ {
                deny all;
         }
         location ~ \.php$ {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                include        fastcgi_params; #如果文件单独写出来的时候注意文件的位置
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
         }
         location /index.php {
                fastcgi_pass 127.0.0.1:9000;
                include        fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root/index.php;
         }
         error_page 404 /404.html;
    }
```