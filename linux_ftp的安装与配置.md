layout: '[layout]'
title: Linux FTP的安装与配置
date: 2015-11-06 16:57:19
tags: [linux]
---
# Linux FTP的安装与配置


## ONE.安装

```sh
    # yum install vsftpd
```

## TWO.开启和关闭FTP

```sh
    # service vsftpd start    //开启：
    # service vsftpd stop     //关闭：
```
## THREE.在linux中添加ftp用户，并设置相应的权限，操作步骤如下

- 3.1环境：
        ftp为vsftp 被限制用户名为test。被限制路径为/home/test
    
- 3.2建用户：在root用户下：

```sh
        # useradd -d /home/test test //增加用户test，并制定test用户的主目录为/home/test 
        # passwd test //为test设置密码
```

- 3.3更改用户相应的权限设置：

```sh
        # usermod -s /sbin/nologin test //限定用户test不能telnet，只能ftp 
        # usermod -s /sbin/bash test //用户test恢复正常 
        # usermod -d /test test //更改用户test的主目录为/test
```

- 3.4限制用户只能访问/home/test，不能访问其他路径

```sh
        # /etc/vsftpd/vsftpd.conf //修改 如下：
```

```
            chroot_list_enable=YES //限制访问自身目录
            //(default follows)
            chroot_list_file=/etc/vsftpd/vsftpd.chroot_list
```

  - 编辑 vsftpd.chroot_list文件，将受限制的用户添加进去，每个用户名一行
  - 改完配置文件，不要忘记重启vsFTPd服务器。

```sh
        # /etc/init.d/vsftpd restart
```

- 3.5如果需要允许用户修改密码，但是又没有telnet登录系统的权限

```sh
        # usermod -s /usr/bin/passwd test //用户telnet后将直接进入改密界面
```