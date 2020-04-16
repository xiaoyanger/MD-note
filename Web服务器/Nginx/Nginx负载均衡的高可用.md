layout: '[layout]'
title: Nginx 负载均衡的高可用
date: 2016-01-15 11:25:37
tags: [nginx,linux,lnmp,keepalived,]
---
# 前期准备：

A服务器：192.168.1.4（主，也就是做负载均衡的服务器--nginx+keepalived）
B服务器：192.168.1.201（webserver）
C服务器：192.168.1.254（webserver）
D服务器：192.168.1.3（从，当主服务器无法提供负载均衡服务时，则自动切换到这台服务器为用户提供负载均衡服务，全部都是自动化的--nginx+keepalived）

两台接入服务器公用一个虚拟IP（VIP）：192.168.1.6(预先留存即可，不是服务器的ip)


# 部署思路：

- A服务器做为主负载均衡服务器，D服务器作为从负载服务器。域名直接解析到VIP（192.168.1.6）上，由A服务器负载均衡到B服务器（192.168.1.201）与C服务器（192.168.1.254）上。
- 如果A服务器出现故障，自动由D服务器接管，A服务器恢复以后，自动由A服务器接管负载均衡。


# 域名解析:

- 由于是测试环境，域名就随便使用一个api.zhezi.com用作测试，因为这个域名正常解析到生产环境，所以api.zhezi.com的解析要在hosts文件设置。
- 打开：C:\Windows\System32\drivers\etc\hosts
- 在末尾添加如下内容：192.168.1.6 api.zhezi.com
- 保存退出
- 然后打开DOS窗口（开始-运行-输入cmd-回车）
- ping下看看是否已设置成功
- ps:
- 修改hosts文件也可以使用第三方工具修该
- 进入DOS窗口也可以使用windows键+R打开运行窗口，然后输入cmd-回车进行验证hosts文件修改是否生效


# LNMP环境安装及项目的部署不在这里记录

在我的博客中存在对应的部署文档

# 安装keepalived:

```sh
    #yum -y install gcc pcre-devel zlib-devel openssl-devel nmap*
    #cd /home/tools/
    #wget http://www.keepalived.org/software/keepalived-1.2.17.tar.gz
    #tar -zxvf keepalived-1.2.17.tar.gz
    #cd keepalived-1.2.17
    #./configure
    #make
    #make install
    #cp /usr/local/etc/rc.d/init.d/keepalived /etc/rc.d/init.d/ //复制服务启动脚本到，以便可以通过service控制keepalived服务
    #cp /usr/local/etc/sysconfig/keepalived /etc/sysconfig/ //复制keepalived服务脚本到默认的地址，也通过修改init.d/keepalived文件中的相应配置实现
    #mkdir /etc/keepalived
    #cp /usr/local/etc/keepalived/keepalived.conf /etc/keepalived/ //复制默认配置文件到默认路径，其实也可以在/etc/init.d/keepalived中设置路径
    #cp /usr/local/sbin/keepalived /usr/sbin/
    #echo "/usr/local/nginx/sbin/nginx" >> /etc/rc.local
    #echo "/etc/init.d/keepalived start" >> /etc/rc.local
```

### 配置


-  A服务器和D服务器nginx的配置（A服务器和D服务器的配置完全一致）主要是配置/usr/local /nginx/conf/nginx.conf。其中多域名指向是通过虚拟主机（配置http下面的server）实现；同一域名的不同虚拟目录通过每个 server下面的不同location实现；到后端的服务器在http下面配置upstream后在server或location中通过 proxypass引用。要实现前面规划的接入方式，配置如下：

-  打开nginx.conf，文件位置在nginx安装目录的conf目录下。在http模块内加入如下内容

```
    upstream api.zhezi.com {
         server 192.168.1.201:80;
         server 192.168.1.254:80;
    }
    server{
        listen 80;
        server_name api.zhezi.com;
        location / {
            proxy_pass http://api.zhezi.com;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
```

-  A服务器（master）keepalived的配置：

```sh
    #vim /etc/keepalived/keepalived.conf
```

```
    ! Configuration File for keepalived
    
    global_defs {//全局配置
        notification_email { //#通知email，根据实际情况配置
            mazhenguo@xxtao.com
            mazhenguo88@163.com
        }
        notification_email_from Alexandre.Cassen@firewall.loc
        smtp_server smtp.exmail.qq.com
        smtp_connect_timeout 30
        router_id nginx_master // #节点名标识，主要用于通知中
    }
    
    vrrp_instance VI_1 {
        state MASTER //配置为主服务器
        interface eth0//通讯网卡（真实网卡名字）
        virtual_router_id 51 路由标识
        priority 101 //优先级，0-254
        advert_int 1 //通知间隔，实际部署时可以设置小一点，减少延时
        authentication {
            auth_type PASS
            auth_pass 1111 //验证密码，用于通讯主机间验证
        }
        virtual_ipaddress {
            192.168.1.6 //虚拟ip，可以定义多个
        }
    }
```

    其他配置项默认不变



-  D服务器（backup）keepalived的配置：

```sh
    #vim /etc/keepalived/keepalived.conf
```

```
    ! Configuration File for keepalived
    
    global_defs {
        notification_email { //通知email，根据实际情况配置
            mazhenguo@xxtao.com
            mazhenguo88@163.com
        }
        notification_email_from Alexandre.Cassen@firewall.loc
        smtp_server smtp.exmail.qq.com
        smtp_connect_timeout 30
        router_id nginx_backup //节点名标识，主要用于通知中
    }
    
    vrrp_instance VI_1 {
        state BACKUP //配置为从服务器
        interface eno16777728 //从服务器的通讯网卡
        virtual_router_id 51 //路由标识，和主服务器相同
        priority 99 //优先级，小于主服务器即可
        advert_int 1 //这里是接受通知间隔，与主服务器要设置相同
        authentication {
            auth_type PASS
            auth_pass 1111 //验证密码，与主服务器相同
        }
        virtual_ipaddress {
             192.168.1.6 //虚拟IP，也要和主服务器相同
        }
    }
```

### 验证：


    -  先后在主、从服务器上启动keepalived（先启动主，后启动从）:
```sh
    #/etc/init.d/keepalived start
```

- 在主服务器上查看是否已经绑定了虚拟IP：

```
    #ip addr（主服务器）
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:a8:8a:95 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.4/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet 192.168.1.6/32 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fea8:8a95/64 scope link
        valid_lft forever preferred_lft forever
    #ip addr (从服务器)
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:bb:e0:f9 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.3/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:febb:e0f9/64 scope link
        valid_lft forever preferred_lft forever
```

- 停止主服务器上的keepalived，然后在从服务器上查看是否已经绑定了虚拟IP:

```
    #/etc/init.d/keepalived stop （主服务器）
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:a8:8a:95 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.4/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fea8:8a95/64 scope link
        valid_lft forever preferred_lft forever
    #ip addr
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:bb:e0:f9 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.3/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet 192.168.1.6/32 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:febb:e0f9/64 scope link
        valid_lft forever preferred_lft forever
```
- 启动主服务器上的keepalived，看看主服务器能否重新接管虚拟IP

```
    #/etc/init.d/keepalived start (主服务器)
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:a8:8a:95 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.4/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet 192.168.1.6/32 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:fea8:8a95/64 scope link
        valid_lft forever preferred_lft forever
    #ip addr（从服务器）
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
        2: eno16777728: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:bb:e0:f9 brd ff:ff:ff:ff:ff:ff
        inet 192.168.1.3/24 brd 192.168.1.255 scope global eno16777728
        valid_lft forever preferred_lft forever
        inet6 fe80::20c:29ff:febb:e0f9/64 scope link
        valid_lft forever preferred_lft forever
```

### B、C服务器的配置按照正常的web服务器配置即可，这里不再做记录


### 让keepalived监控NginX的状态（以下服务器在主服务器上操作）

```经过前面的配置，如果主服务器的keepalived停止服务，从服务器会自动接管VIP对外服务；一旦主服务器的keepalived恢复，会重新接管VIP。 但这并不是我们需要的，我们需要的是当NginX停止服务的时候能够自动切换。
keepalived支持配置监控脚本，我们可以通过脚本监控NginX的状态，如果状态不正常则进行一系列的操作，最终仍不能恢复NginX则杀掉keepalived，使得从服务器能够接管服务。
如何监控NginX的状态
最简单的做法是监控NginX进程，更靠谱的做法是检查NginX端口，最靠谱的做法是检查多个url能否获取到页面。
如何尝试恢复服务
如果发现NginX不正常，重启之。等待3秒再次校验，仍然失败则不再尝试。
根据上述策略很容易写出监控脚本。这里使用nmap检查nginx端口来判断nginx的状态，记得要首先安装nmap。
https://nmap.org/dist/nmap-7.01.tar.bz2   nmap下载地址（linux无法正常解压，需要win支持，可以自己找合适的安装包）
监控脚本如下:```

```sh
    #mkdir /home/crontab
    #vim /home/crontab/nginx_status.sh
    
    #!/bin/sh
    # check nginx server status
    NGINX=/usr/local/nginx/sbin/nginx
    PORT=80
    
    nmap localhost -p $PORT | grep "$PORT/tcp open"
    #echo $?
    if [ $? -ne 0 ];then
    $NGINX -s stop
    $NGINX
    sleep 3
    nmap localhost -p $PORT | grep "$PORT/tcp open"
    [ $? -ne 0 ] && /etc/init.d/keepalived stop
    fi
```
   wq保存退出
```sh
    #cd /home/crontab/
    #chmod +x nginx_status.sh
```

    假设上述脚本放在/home/crontab/nginx_status.sh，则keepalived.conf中增加如下配置：
```sh
    #vim /etc/keepalived/keepalived.conf
 ```  
   以下内容在router_id之后首层增加
```
    vrrp_script chk_http_port {
        script "/home/crontab/nginx_status.sh"
        interval 2
        weight 2
    }
   //以下内容在virtual_ipaddress上面增加
   
    track_script {
        chk_http_port
    }
```


### 测试：

- 将主服务器的nginx停止掉，同时去查询nginx的进程，大概1秒钟nginx会重新启动，说明keepalived重启nginx脚本生效了
- 将主服务器关机，看看从服务器是否可以接管主服务器的负载均衡服务。也可以一致刷新页面，看服务是否中断，结果是正常的业务不会中断。
- 启动主服务器后，使用ip addr查看主服务器，发现主服务器自动接管了负载均衡服务。
（注意）测试的时候不可以着急，keepalived进程加载可能需要半分钟的时间作为缓冲，所以，等待半分钟
nginx负载均衡的高可用完成。
