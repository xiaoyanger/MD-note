layout: '[layout]'
title: Https站点搭建
date: 2016-1-8 00:00:00
tags: [linux,https]
---
Https站点搭建

1.生成对应的crt和key文件【上线的时候是需要向“有关部门”申请正式的文件】，并进行配置
  
  ```sh
    [root@localhost ~]# cd /etc/pki/tls/certs
    [root@localhost certs]# make server.key
    umask 77 ; \
    /usr/bin/openssl genrsa -aes128 2048 > server.key
    Generating RSA private key, 2048 bit long modulus
    ......................................................++++++
    .............++++++
    e is 61251 (0x10001)
    Enter pass phrase:# 设置密码
    Verifying - Enter pass phrase:# 重复
    # remove passphrase from private key
    [root@localhost certs]# openssl rsa -in server.key -out server.key
    Enter pass phrase for server.key:# input passphrase
    writing RSA key
    [root@localhost certs]#
    [root@localhost certs]# make server.csr
    umask 77 ; \
    /usr/bin/openssl req -utf8 -new -key server.key -out server.csr
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [XX]:CN #country
    State or Province Name (full name) [e]:Beijing   #state
    Locality Name (eg, city) [Default City]:Beijing  #city
    Organization Name (eg, company) [Default Company Ltd]:Test   #company
    Organizational Unit Name (eg, section) []:Test Haha   #department
    Common Name (eg, your server's hostname) []:www.test.com   #server's FQDN
    Email Address []:admin@test.com # email address
    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:# Enter
    An optional company name []:# Enter
    [root@localhost certs]#
    [root@localhost certs]# openssl x509 -in server.csr -out server.crt -req -signkey server.key -days 3650
    Signature ok
    subject=/C=CN/ST=Beijing/L=Beijing/O=Test/OU=Test Haha/CN=www.test.com,/emailAddress=admin@test.com
    Getting Private key
    [root@localhost certs]# chmod 400 server.*    #更改400权限
    [root@localhost certs]# chown www.www server.*    #将文件所属权给nginx的用户
```

2.配置nginx的服务器

```sh
    server {
        listen       443 ssl;
            ssl_certificate      /etc/pki/tls/certs/server.crt;
            ssl_certificate_key  /etc/pki/tls/certs/server.key;
        server_name  loc.https.cn;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /home/www/https;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /home/www/https;
        }
       location ~ \.php$ {
            root /home/www/https;
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
```

3.将对应域名的80端口进行重定向，定向到对应的https域名上

```sh
    server {
        listen 80;
        server_name loc.https.cn;
        rewrite ^(.*) https://loc.https.cn;
    }
```

完成：享受你的https旅程吧
