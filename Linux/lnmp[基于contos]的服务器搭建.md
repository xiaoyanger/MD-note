layout: '[layout]'
title: lnmp服务器搭建
date: 2015-11-11 00:00:00
tags:  [nginx,linux,php,mysql]
---

nginx+mysql+php=>lnmp服务器搭建
<a href="http://www.lmyw.net.cn/?p=487">[转自老马运维，十分感谢]</a>
centos:7.1.1503 64位系统
nginx:1.8.0
mysql:5.6.24
php:5.6.8

---

# 根据生产环境安装操作系统（centos 7.1.1503 64位系统），安装完成后，使用Xshell通过ssh协议连接服务器。
## ssh 用户名@IP+回车+输入密码后登录系统。
```sh
    $&: mkdir -p /home/tools
    $&: cd /home/tools
```
## 将软件包上传到/home/tools

## 修改网卡的配置文件

```sh
    $&: vim /etc/sysconfig/network-scripts/ifcfg-ens32 //ifcfg-ens32是网卡的设备名称，每个机器的设备名不一样，修改的时候改成自己机器的设备名称
    HWADDR=00:1e:37:25:92:77 //改成自己机器的MAC地址
    TYPE=Ethernet
    BOOTPROTO=static
    DEFROUTE=yes
    PEERDNS=yes
    PEERROUTES=yes
    USERCTL=no
    NM_CONTROLLED=no
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_PEERDNS=yes
    IPV6_PEERROUTES=yes
    IPV6_FAILURE_FATAL=no
    NAME=eno16777736
    ONBOOT=yes
    IPADDR=192.168.1.91
    NETMASK=255.255.255.0
    GATEWAY=192.168.1.1
    DNS1=202.106.0.20
    $&: systemctl -f restart NetworkManager
```

## 关键配置：

```
    TYPE=Ethernet
    BOOTPROTO=static
    NAME=eno16777736
    ONBOOT=yes
    IPADDR=192.168.117.128
    NETMASK=255.255.255.0
    GATEWAY=192.168.117.2
    DNS1=192.168.117.2
    $&: cat /etc/resolv.conf
    nameserver 192.168.117.2
```
## 创建软件包存储目录

```sh
    $&: cd /home/
    $&: mkdir tools
    $&: cd tools/
```

## 安装系统程序

```sh
    $&: yum -y install wget
    $&: yum -y install vim*
    $&: yum -y install ntpdate
    $&: yum -y install mlocate
    $&: updatedb
    $&: yum -y install lrzsz* //可安装可不安装
    $&: yum -y install openssl*
    $&: yum -y install pcre-devel
    $&: yum -y install ncurses-devel
    $&: yum -y install gcc python-devel
    $&: service iptables stop
```

## centos 7.0执行如下内容

```sh
    $&: systemctl start firewalld.service //启动firewall
    $&: systemctl stop firewalld.service //停止firewall
    $&: systemctl disable firewalld.service //禁止firewall开机启动
    $&: chmod +x /etc/rc.d/rc.local //设置开机启动项脚本权限
```

## 时间同步

```sh
    $&: /usr/sbin/ntpdate us.pool.ntp.org | logger -t NTP // 0-59/10 * * * * /usr/sbin/ntpdate us.pool.ntp.org | logger -t NTP(每隔10分钟同步一下时钟)
```

## 下载nginx、pcre、zlib

```sh
    $&: wget http://nginx.org/download/nginx-1.8.0.tar.gz
    $&: wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre2-10.00.tar.gz
    $&: wget http://zlib.net/zlib-1.2.8.tar.gz
```

## 安装pcre

```sh
    $&: tar -zxvf pcre2-10.00.tar.gz
    $&: cd pcre2-10.00/
    $&: ./configure
    $&: make
    $&: make install
    $&: cd /home/tools/
```

## 安装zlib

```sh
    $&: tar -zxvf zlib-1.2.8.tar.gz
    $&: cd zlib-1.2.8
    $&: ./configure
    $&: make
    $&: make install
```

## 安装第三方软件源

```sh
    $&: wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm
    $&: rpm -ivh rpmforge-release-0.5.2-2.el5.rf.x86_64.rpm
```

# 安装nginx

```sh
    $&: cd /home/tools/
    $&: tar -zxvf nginx-1.8.0.tar.gz
    $&: cd nginx-1.8.0/
    $&: /usr/sbin/groupadd www
    $&: /usr/sbin/useradd -g www www
    $&: ./configure –user=www –group=www –with-http_stub_status_module –with-http_gzip_static_module –prefix=/usr/local/nginx
    $&: ./configure –user=www –group=www –with-http_stub_status_module –with-http_gzip_static_module –with-http_ssl_module –prefix=/usr/local/nginx //支持https
    $&: make
    $&: make install
    $&: mkdir -p /home/logs
    $&: mkdir -p /home/logs/api.m.woniuanjia.com
    $&: mkdir -p /home/logs/www.m.woniuanjia.com
    $&: mkdir -p /home/logs/demo.woniuanjia.com
    $&: mkdir -p /home/app/api.m.woniuanjia.com
    $&: mkdir -p /home/app/www.m.woniuanjia.com
    $&: mkdir -p /home/app/demo.woniuanjia.com
    $&: mkdir /home/logs/nginx-log/
    $&: chmod +w /home/logs
    $&: chmod +w /home/app/api.m.woniuanjia.com/
    $&: chmod +w /home/app/www.m.woniuanjia.com/
    $&: chmod +w /home/app/demo.woniuanjia.com/
    $&: chmod +w /home/logs/nginx-log/
    $&: chown -R www:www /home/logs/
    $&: chown -R www:www /home/app/
    $&: chown -R www:www /home/logs/nginx-log/
    $&: mkdir -p /home/nginx/logs/
    $&: vim /etc/passwd
    www:x:500:500::/home/www:/sbin/nologin
    $&: cp /usr/local/nginx/conf/nginx.conf /usr/local/nginx/conf/nginx.conf.bak
    $&: cat /dev/null >/usr/local/nginx/conf/nginx.conf
    $&: vim /usr/local/nginx/conf/nginx.conf
```

```
        user www www;
        worker_processes 2;
        worker_cpu_affinity 0101 1010;
        error_log /home/logs/nginx-log/error.log crit;
        pid /usr/local/nginx/logs/nginx.pid;
        worker_rlimit_nofile 65535;
        events {
            use epoll;
            worker_connections 65535;
        }
        http {
            include mime.types;
            default_type application/octet-stream;
            charset utf-8;
            log_format access ‘$remote_addr – $remote_user [$time_local] “$request” $status $body_bytes_sent “$http_referer” “$http_user_agent” “$http_x_forwarded_for” host’;
            server_names_hash_bucket_size 128;
            client_header_buffer_size 4k;
            large_client_header_buffers 4 4k;
            sendfile on;
            tcp_nopush on;
            tcp_nodelay on;
            server_tokens off;
            keepalive_timeout 60;
            gzip on;
            gzip_min_length 1k;
            gzip_buffers 4 16k;
            gzip_http_version 1.1;
            gzip_comp_level 4;
            $&: gzip_types text/plain application/x-javascript text/css application/xml;
            gzip_types text/plain application/x-javascript text/css;
            gzip_vary on;
            server {
                listen 80 default;
                return 200;
            }
        }
```

- 以上为默认增加的nginx配置
- 以下为项目在线host配置

```
   $&:  host.woniu.com #
   
        server {
            listen 80;
            server_name host.woniu.com 192.168.1.91;
            root /home/app/host.woniu.com/;
        }
```

- 以下为新增的nginx配置

```
   $&: api.m.woniuanjia.com#
 ```

 ```
    server {
        listen 80;
        server_name api.m.woniuanjia.com;
        charset utf-8;
        root /home/app/api.m.woniuanjia.com/v1.0/web/;
        location / {
            index index.php index.html index.htm;
        }
        location /v1.0/ {
            index index.php;
            alias /home/app/api.m.woniuanjia.com/v1.0/web/;
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root html;
        }
        location ~ ^/v1.0/(.*\.php)$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME /home/app/api.m.woniuanjia.com/v1.0/web/$1;
            include fastcgi_params;
        }
        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
        access_log /home/logs/api.m.woniuanjia.com/access.log access;
        error_log /home/logs/api.m.woniuanjia.com/error.log info;
    }
```

```sh
   $&: www.m.woniuanjia.com#
```

```
    server {
        listen 80;
        server_name www.m.woniuanjia.com;
        charset utf-8;
        root /home/app/www.m.woniuanjia.com/v1.0/;
        location / {
            index index.php index.html index.htm;
        }
        location /v1.0/ {
            index index.php;
            alias /home/app/www.m.woniuanjia.com/v1.0/;
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root html;
        }
        location ~ ^/v1.0/(.*\.php)$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME /home/app/www.m.woniuanjia.com/v1.0/$1;
            include fastcgi_params;
        }
        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
        access_log /home/logs/www.m.woniuanjia.com/access.log access;
        error_log /home/logs/www.m.woniuanjia.com/error.log info;
    }
```

```sh
   $&: demo.woniuanjia.com#
```

```
    server {
        listen 80;
        server_name demo.woniuanjia.com;
        root /home/app/demo.woniuanjia.com/v1.0/;
        location / {
                index index.php index.html index.htm;
                if (!-e $request_filename) {
                    rewrite ^/(.*)$ /index.php?$1 last;
                    break;
            }
        }
        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
        access_log /home/logs/demo.woniuanjia.com/access.log access;
        error_log /home/logs/demo.woniuanjia.com/error.log info;
    }
```

```sh
   $&: img.woniuanjia.com#
```

```sh
    server {
        listen 80;
        server_name img.m.woniuanjia.com;
        root /home/app/img.m.woniuanjia.com/;
        location / {
            index index.php index.html index.htm;
                if (!-e $request_filename) {
                rewrite ^/(.*)$ /index.php?$1 last;
                break;
            }
        }
    }
```

```sh
   $&: finish

    $&: vim /etc/security/limits.conf
```

```
    *              soft    nproc   16384
    *              hard    nproc   16384
    *              soft    nofile  4096
    *              hard    nofile  65536
    *              soft    stack   10240
```

- 修改完之后退出重新登录生效


```sh
    $&: ulimit -n
    $&: ulimit -a
    $&: mkdir /home/crontab
    $&: vim /home/crontab/cut_nginx_log.sh
    $&: !/bin/bash
    $&: auth peter
    $&:  定时切割Nginx日志的脚本
    $&:  每小时生产一份日志
    $&:  如果是每月1号,则将上月日志归档
```

```
        today=`date +%e`
        monthlog=`date -d ‘-1 month’ +%Y%m`
        hour=`date +%k`
        hour_accesslogname=`date +”%Y%m%d%H” –date=”-1 hour”`
        day_errorlogname=`date +”%Y%m%d” –date=”-1 day”`
```

```sh
    $&:  The Nginx logs path
        $&: 切割api.m.woniuanjia.com日志
            logs_path=”/home/logs/api.m.woniuanjia.com/”
            mv ${logs_path}access.log ${logs_path}access-${hour_accesslogname}.log
            if [ $hour -eq 0 ]; then
            mv ${logs_path}error.log ${logs_path}error-${day_errorlogname}.log
            fi
            if [ $today -eq 1 ]; then
            if [ ! -e ${logs_path}${monthlog} ]; then
            mkdir ${logs_path}${monthlog}
            fi
            mv ${logs_path}access-${monthlog}*.log ${logs_path}${monthlog}
            mv ${logs_path}error-${monthlog}*.log ${logs_path}${monthlog}
            fi
        $&: 切割www.m.woniuanjia.com日志
            logs_path=”/home/logs/www.m.woniuanjia.com/”
            mv ${logs_path}access.log ${logs_path}access-${hour_accesslogname}.log
            if [ $hour -eq 0 ]; then
            mv ${logs_path}error.log ${logs_path}error-${day_errorlogname}.log
            fi
            if [ $today -eq 1 ]; then
            if [ ! -e ${logs_path}${monthlog} ]; then
            mkdir ${logs_path}${monthlog}
            fi
            mv ${logs_path}access-${monthlog}*.log ${logs_path}${monthlog}
            mv ${logs_path}error-${monthlog}*.log ${logs_path}${monthlog}
            fi
        $&: 切割demo.woniuanjia.com日志
            logs_path=”/home/logs/demo.woniuanjia.com/”
            mv ${logs_path}access.log ${logs_path}access-${hour_accesslogname}.log
            if [ $hour -eq 0 ]; then
            mv ${logs_path}error.log ${logs_path}error-${day_errorlogname}.log
            fi
            if [ $today -eq 1 ]; then
            if [ ! -e ${logs_path}${monthlog} ]; then
            mkdir ${logs_path}${monthlog}
            fi
            mv ${logs_path}access-${monthlog}*.log ${logs_path}${monthlog}
            mv ${logs_path}error-${monthlog}*.log ${logs_path}${monthlog}
            fi
            kill -USR1 `cat /usr/local/nginx/logs/nginx.pid`
            $&: chmod +x /home/crontab/cut_nginx_log.sh
            $&: chown www:www /home/crontab/cut_nginx_log.sh
            $&: crontab -e
            00 10 * * * /bin/bash /home/crontab/cut_nginx_log.sh
    $&: vim /etc/rc.d/rc.local
    /usr/local/nginx/sbin/nginx
    $&: /usr/local/nginx/sbin/nginx -t
    $&: /usr/local/nginx/sbin/nginx
    $&: /usr/local/nginx/sbin/nginx -s reload
```


## 安装mysql


```sh
    $&: cd /home/tools/
    $&: wget http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.15.tar.gz
    $&: wget http://www.cmake.org/files/v2.8/cmake-2.8.4.tar.gz
    $&: wget http://ftp.gnu.org/gnu/bison/bison-3.0.4.tar.gz
    $&: tar zxvf cmake-2.8.4.tar.gz
    $&: cd cmake-2.8.4
    $&: ./bootstrap
    $&: gmake
    $&: gmake install
    $&: cd ..
    $&: tar zxvf bison-3.0.4.tar.gz
    $&: cd bison-3.0.4/
    $&: ./configure
    $&: make
    $&: make install
    $&: cd ..
    $&: /usr/sbin/groupadd mysql
    $&: /usr/sbin/useradd -g mysql mysql
    $&: vim /etc/passwd
    mysql:x:501:501::/home/mysql:/sbin/nologin
    $&: tar -zxvf mysql-5.6.24.tar.gz
    $&: cd mysql-5.6.24
    $&: cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
        -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
        -DDEFAULT_CHARSET=utf8 \
        -DDEFAULT_COLLATION=utf8_general_ci \
        -DWITH_EXTRA_CHARSETS:STRING=utf8,gbk \
        -DWITH_MYISAM_STORAGE_ENGINE=1 \
        -DWITH_INNOBASE_STORAGE_ENGINE=1 \
        -DENABLED_LOCAL_INFILE=1 \
        -DMYSQL_DATADIR=/usr/local/mysql/data
```


   PS:
   以上参数等说明:
 ```

```  
       DCMAKE_INSTALL_PREFIX=/usr/local/mysql #mysql安装的主目录，默认为/usr/local/mysql
       DMYSQL_DATADIR=/usr/local/mysql/data #mysql数据库文件的存放目录，可以自定义
       DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock #系统Socket文件（.sock）设置,基于该文件路径进行Socket链接，必须为绝对路径
       DSYSCONFDIR=/etc #mysql配置文件 my.cnf的存放地址，默认为/etc下
       DMYSQL_TCP_PORT=3306 #数据库服务器监听端口，默认为3306
       DENABLED_LOCAL_INFILE=1 #允许从本地导入数据
       DWITH_READLINE=1 #快捷键功能
       DWITH_SSL=yes #支持 SSL
       DMYSQL_USER=mysql #默认为mysql
   //下面3个是数据库编码设置
   
       DEXTRA_CHARSETS=all #安装所有扩展字符集，默认为all
       DDEFAULT_CHARSET=utf8 #使用 utf8 字符
       DDEFAULT_COLLATION=utf8_general_ci #校验字符
   //下面5个是数据库存储引擎设在
   
       DWITH_MYISAM_STORAGE_ENGINE=1 #安装 myisam 存储引擎
       DWITH_INNOBASE_STORAGE_ENGINE=1 #安装 innodb 存储引擎
       DWITH_ARCHIVE_STORAGE_ENGINE=1 #安装 archive 存储引擎
       DWITH_BLACKHOLE_STORAGE_ENGINE=1 #安装 blackhole 存储引擎
       DWITH_PARTITION_STORAGE_ENGINE=1 #安装数据库分区
```

```sh
    $&: make
```

```
---- 可能报错

          FATAL ERROR: Could not find ./bin/my_print_defaults
          If you compiled from source, you need to run 'make install' to
          copy the software into the correct location ready for operation.
          If you are using a binary release, you must either be at the top
          level of the extracted archive, or pass the --basedir option
          pointing to that location.

---- 解决方案
```


```sh

    #/usr/local/bin/mysql_install_db --user=mysql --basedir=/usr/local/ --datadir=/var/db/mysql &
    $&: make install
    $&: chmod +w /usr/local/mysql
    $&: chown -R mysql:mysql /usr/local/mysql #改变目录拥有者与所属组
    $&: ln -s /usr/local/mysql/lib/libmysqlclient.so.16 /usr/lib/libmysqlclient.so.16
    $&: ln -s /usr/local/mysql/lib/libmysqlclient.so.16 /usr/lib64/libmysqlclient.so.16
    $&: cd support-files/
    $&: cp my-default.cnf /etc/my.cnf #选择默认配置文件 适合大型服务器
    cp：是否覆盖”/etc/my.cnf”？ y
    $&: cp mysql.server /etc/init.d/mysqld #复制启动文件
    $&: vim /etc/my.cnf
```

在[mysqld]下面添加


```sh
    basedir = /usr/local/mysql/
    datadir = /usr/local/mysql/data
    log-error = /usr/local/mysql/mysql_error.log
    pid-file = /usr/local/mysql/data/mysql.pid
    default-storage-engine=INNODB
    user = mysql
    $&: /usr/local/mysql/scripts/mysql_install_db \
    –basedir=/usr/local/mysql \
    –datadir=/usr/local/mysql/data \
    –user=mysql
    $&: chmod +x /etc/init.d/mysqld
    $&: vim /etc/init.d/mysqld（编辑此文件，查找并修改以下变量内容：）
    basedir=/usr/local/mysql
    datadir=/usr/local/mysql/data
    $&: chkconfig –add mysqld
    $&: chkconfig –level 345 mysqld on
    $&: export PATH=/usr/local/mysql/bin:$PATH //为MySQL配置环境变量，以后使用起来方便
    $&: service mysqld start
    Starting MySQL. SUCCESS!
    $&: mysql_secure_installation
    NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
    SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!
    In order to log into MySQL to secure it, we’ll need the current
    password for the root user. If you’ve just installed MySQL, and
    you haven’t set the root password yet, the password will be blank,
    so you should just press enter here.
    Enter current password for root (enter for none): 输入mysql的root密码，默认为空
    OK, successfully used password, moving on…
    Setting the root password ensures that nobody can log into the MySQL
    root user without the proper authorisation.
    You already have a root password set, so you can safely answer ‘n’.
    Change the root password? [Y/n] y //是否修改mysql的密码，选择y
    New password: 输入mysql的密码
    Re-enter new password: 重复输入mysql的密码
    Password updated successfully!//密码修改成功
    Reloading privilege tables..
    … Success!
    By default, a MySQL installation has an anonymous user, allowing anyone
    to log into MySQL without having to have a user account created for
    them. This is intended only for testing, and to make the installation
    go a bit smoother. You should remove them before moving into a
    production environment.
    Remove anonymous users? [Y/n] y //删除匿名用户
    … Success!
    Normally, root should only be allowed to connect from ‘localhost’. This
    ensures that someone cannot guess at the root password from the network.
    Disallow root login remotely? [Y/n] y //禁止root远程登录
    … skipping.
    By default, MySQL comes with a database named ‘test’ that anyone can
    access. This is also intended only for testing, and should be removed
    before moving into a production environment.
    Remove test database and access to it? [Y/n] y //删除测试数据库和访问它
    – Dropping test database…
    ERROR 1010 (HY000) at line 1: Error dropping database (can’t rmdir ‘./test/’, errno: 17)
    … Failed! Not critical, keep moving…
    – Removing privileges on test database…
    … Success!
    Reloading the privilege tables will ensure that all changes made so far
    will take effect immediately.
    Reload privilege tables now? [Y/n] y //现在重载授权表
    … Success!
    Cleaning up…
    All done! If you’ve completed all of the above steps, your MySQL
    installation should now be secure.
    
    Thanks for using MySQL

```

修改mysql密码-----SET PASSWORD FOR 'root'@'localhost' = PASSWORD('root123');
查看及修改mysql最大连接数

```sh
    $&: ps -axef | grep mysqld –c
    mysql> show engines; //看你的mysql现在已提供什么存储引擎:
    +——————–+———+—————————————————————-+————–+——+————+
    | Engine | Support | Comment | Transactions | XA | Savepoints |
    +——————–+———+—————————————————————-+————–+——+————+
    | MyISAM | YES | MyISAM storage engine | NO | NO | NO |
    | CSV | YES | CSV storage engine | NO | NO | NO |
    | MRG_MYISAM | YES | Collection of identical MyISAM tables | NO | NO | NO |
    | BLACKHOLE | YES | /dev/null storage engine (anything you write to it disappears) | NO | NO | NO |
    | MEMORY | YES | Hash based, stored in memory, useful for temporary tables | NO | NO | NO |
    | FEDERATED | NO | Federated MySQL storage engine | NULL | NULL | NULL |
    | ARCHIVE | YES | Archive storage engine | NO | NO | NO |
    | PERFORMANCE_SCHEMA | YES | Performance Schema | NO | NO | NO |
    | InnoDB | DEFAULT | Supports transactions, row-level locking, and foreign keys | YES | YES | YES |
    +——————–+———+—————————————————————-+————–+——+————+
    9 rows in set (0.00 sec)
    mysql> show variables like ‘%storage_engine%’;//看你的mysql当前默认的存储引擎:
    +—————————-+——–+
    | Variable_name | Value |
    +—————————-+——–+
    | default_storage_engine | InnoDB |
    | default_tmp_storage_engine | InnoDB |
    | storage_engine | InnoDB |
    +—————————-+——–+
3 rows in set (0.00 sec)
```


你要看某个表用了什么引擎(在显示结果里参数engine后面的就表示该表当前用的存储引擎):

```sh
    mysql> show create table 表名;
     
    Mysql>show variables like ‘max_connections’;
    Mysql> set GLOBAL max_connections=2000;
    Mysql> show variables like ‘max_connections’;
    Mysql> show processlist;//查看mysql的数据库连接
    $&: Vim /etc/my.cnf //在mysqld中增加如下内容：
    max_connections=500
    back_log=100
    $&: mkdir -p /var/lib/mysql
    $&: ln -s /usr/local/mysql/mysql.sock /var/lib/mysql/mysql.sock
```


# 安装php
```sh
    $&: cd /home/tools/
    $&: tar -zxvf php-5.6.8.tar.gz
    $&: yum -y install gd curl curl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel mysql mysql-devel
    $&: cd php-5.6.8
    $&: ./configure –prefix=/usr/local/php –enable-fpm –enable-debug –with-gd –with-jpeg-dir –with-png-dir –with-freetype-dir –enable-mbstring –with-curl –with-mysql=/usr/local/mysql/ –with-mysqli=/usr/local/mysql/bin/mysql_config
    $&: ./configure –prefix=/usr/local/php –enable-fpm –disable-debug –with-gd –with-jpeg-dir –with-png-dir –with-freetype-dir –enable-mbstring –with-curl –with-mysql=/usr/local/mysql/ –with-mysqli=/usr/local/mysql/bin/mysql_config
```
   只有在开发php扩展库的时候需要开启–enable-debug，生产环境不需要，否则会影响性能。开发人员无法安装zend debug。


```sh
    $&: make
    $&: make install
    $&: cp php.ini-development /usr/local/php/lib/php.ini
    $&: cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
    $&: vim /usr/local/php/lib/php.ini
    修改927行，修改之后如下：date.timezone = Asia/Chongqing
    $&: vim /usr/local/php/etc/php-fpm.conf
    修改;pid = run/php-fpm.pid 为pid = run/php-fpm.pid
    $&: /usr/local/php/sbin/php-fpm
    验证php和nginx的安装
    $&: cd /home/app/api.m.woniuanjia.com/v1.0/web
    $&: vim a.php
```
```php
        <?php
        phpinfo();
        ?>
```
在浏览器中输入 http://api.m.woniuanjia.com/a.php
看到PHPinfo页面，解析PHP正常

```sh
    $&: ps aux | grep -c php-fpm //查看php-fpm进程数
    $&: /usr/bin/php -i|grep mem //查看运行内存
    $&: /etc/init.d/php-fpm restart //重启php-fpm
    $&: vim /usr/local/php/etc/php-fpm.conf
    pid = run/php-fpm.pid //把注释去掉
    $&: /usr/local/php/sbin/php-fpm //php-fpm 启动php-fpm
    $&: kill -INT `cat /usr/local/php/var/run/php-fpm.pid` //关闭php-fpm
    $&: kill -USR2 `cat /usr/local/php/var/run/php-fpm.pid` //重启php-fpm
    $&: ps aux | grep -c php-fpm //查看php-fpm进程数
```

# 安装redis

```sh
    $&: cd /home/tools/
    $&: wget http://download.redis.io/releases/redis-3.0.1.tar.gz
    $&: tar -zxvf redis-3.0.1.tar.gz
    $&: cd redis-3.0.1/
    $&: make
    make命令执行完成后，会在当前src目录下生成本个可执行文件，分别是redis-server、redis-cli、redis-benchmark、redis-stat，它们的作用如下：
    redis-server：Redis服务器的daemon启动程序
    redis-cli：Redis命令行操作工具。当然，你也可以用telnet根据其纯文本协议来操作
    redis-benchmark：Redis性能测试工具，测试Redis在你的系统及你的配置下的读写性能
    redis-stat：Redis状态检测工具，可以检测Redis当前状态参数及延迟状况。
    $&: cp redis.conf /etc/ //这个文件是redis启动的配置文件
    $&: cd src/
    $&: cp redis-benchmark redis-cli redis-server /usr/bin/ //这个倒是很有用，这样就不用再执行时加上./了，而且可以在任何地方执行
    $&: redis-server /etc/redis.conf //启动redis
    $&: vim /etc/redis.conf
```

   将daemonize no修改为daemonize yes
   保存退出
```sh

    $&: nohup redis-server /etc/redis.conf
    $&: redis-cli shutdown //关闭redis服务
```

    redis服务关闭后，缓存数据会自动dump到硬盘上，硬盘地址为redis.conf中的配置项dbfilename dump.rdb所设定
    $&: redis-cli save 或者 redis-cli -p 6380 save（指定端口） //强制备份数据到磁盘，使用如下命令

# 安装redis.so模块

```sh
    $&: cd /home/tools/
    $&: wget https://github.com/nicolasff/phpredis/archive/master.zip
    $&: unzip master.zip
    $&: cd phpredis-master/
    $&: /usr/local/php/bin/phpize
    $&: ./configure –with-php-config=/usr/local/php/bin/php-config
    $&: make
    $&: make install
    Installing shared extensions: /usr/local/php/lib/php/extensions/debug-non-zts-20131226/
```
    注意redis.so的全路径是：
    /usr/local/php/lib/php/extensions/debug-non-zts-20131226/redis.so
    $&: vim /usr/local/php/lib/php.ini
   手工修改：查找/usr/local/php/lib/php.ini中的extension_dir = "./"
   修改为extension_dir = "/usr/local/php/lib/php/extensions/debug-non-zts-20131226/"
   并在此行后增加以下几行，然后保存：
   
    extension = "redis.so"

# 安装PDO-mysql

```sh
    $&: cd /home/tools/
    $&: wget http://pecl.php.net/get/PDO_MYSQL-1.0.2.tgz
    $&: tar zxvf PDO_MYSQL-1.0.2.tgz
    package2.xml
    PDO_MYSQL-1.0.2/config.m4
    PDO_MYSQL-1.0.2/CREDITS
    PDO_MYSQL-1.0.2/mysql_driver.c
    PDO_MYSQL-1.0.2/mysql_statement.c
    PDO_MYSQL-1.0.2/pdo_mysql.c
    PDO_MYSQL-1.0.2/php_pdo_mysql.h
    PDO_MYSQL-1.0.2/php_pdo_mysql_int.h
    PDO_MYSQL-1.0.2/php_pdo_mysql_sqlstate.h
    package.xml
    $&: cd PDO_MYSQL-1.0.2
    $&: /usr/local/php/bin/phpize
    Configuring for:
    PHP Api Version: 20131106
    Zend Module Api No: 20131226
    Zend Extension Api No: 220131226
    $&: ./configure –with-php-config=/usr/local/php/bin/php-config
    $&: make
```

执行时报错如下：

```
    cc -I -I/usr/local/mysql/include -g -fabi-version=2 -fno-omit-frame-pointer -fno-strict-aliasing -I. -I/home/tools/PDO_MYSQL-1.0.2 -DPHP_ATOM_INC -I/home/tools/PDO_MYSQL-1.0.2/include -I/home/tools/PDO_MYSQL-1.0.2/main -I/home/tools/PDO_MYSQL-1.0.2 -I/usr/local/php/include/php -I/usr/local/php/include/php/main -I/usr/local/php/include/php/TSRM -I/usr/local/php/include/php/Zend -I/usr/local/php/include/php/ext -I/usr/local/php/include/php/ext/date/lib -DHAVE_CONFIG_H -g -O0 -c /home/tools/PDO_MYSQL-1.0.2/pdo_mysql.c -fPIC -DPIC -o .libs/pdo_mysql.o
    In file included from /home/tools/PDO_MYSQL-1.0.2/pdo_mysql.c:31:0:
    /home/tools/PDO_MYSQL-1.0.2/php_pdo_mysql_int.h:25:19: 致命错误：mysql.h：没有那个文件或目录
    $&: include <mysql.h>
    ^
```
编译中断。

    make: *** [pdo_mysql.lo] //错误 1
解决办法：

```sh
    $&:ln -s /usr/local/mysql/include/* /usr/local/include/
    $&: make
    $&: make install
    Installing shared extensions: /usr/local/php/lib/php/extensions/debug-non-zts-20131226/
    $&: vim /usr/local/php/lib/php.ini
    在extension = "redis.so"下面增加
    extension = "pdo_mysql.so"

```

# 安装phpopenssl


```sh
    $&: cd /home/tools/php-5.6.8/ext/openssl
    $&: cp config0.m4 config.m4
    $&: /usr/local/php/bin/phpize
    $&: ./configure --with-php-config=/usr/local/php/bin/php-config
    $&: make
    $&: make install
    Installing shared extensions: /usr/local/php/lib/php/extensions/debug-non-zts-20131226/
    $&: vim /usr/local/php/lib/php.ini

```

    在extension_dir 参数下面增加extension = "openssl.so"
# 保存退出，重启php


```sh
    ＃kill -USR2 `cat /usr/local/php/var/run/php-fpm.pid`
```
