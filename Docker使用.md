layout: '[docker]'
title: docker
date: 2019-01-10 09:35:47
tags: [docker]
categories: [docker]
---
# docker
```
为啥我要用docker呢:
   作为一个程序员,我的电脑是常常会尝试新的电脑系统,代价就是随时随地,我的电脑都有可能崩溃,要装的软件一大堆,就是找不到适合你的电脑系统的软件安装包,崩溃ing...
   docker作为一款跨平台的软件,适配了windows,mac,linux平台,我只需要有自己的docker镜像,然后生成对应的容器,就能在不同平台搭建自己的开发平台,省却了多次配置项目的麻烦,还保证了自己的开发不会因为系统的切换而收到环境问题的影响.
   作为开发,我感觉,这玩意儿极大的提高了我的开发效率
   如下,我吧我最近使用docker的一些经验写下来
```
## linux环境下
### 安装docker
```ssh
    ##更新软件源
    $ sudo apt-get update
    ## 如果源不合适,请自行进行源切换
    ## 安装docker服务
    $ sudo apt-get  install docker.io
    ## 安装docker-compose,用作docker容器的管理
    $ sudo apt-get  install docker-compose
    ## 启动docker服务
    $ sudo service docker start
```
###创建dockerfile文件
```
# 初始化镜像,一般选择官方镜像,可以通过docker search进行搜索,寻找合适的镜像
FROM php:7.1.0-fpm
# 作者相关信息
MAINTAINER *** "***@163.com"
# 设置时区 ENV表示环境变量
ENV TZ=Asia/Shanghai
# RUN 表示运行的命令
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone
# 软件安装就是常用的命令安装,记得关注打出的日志文件,方便调试
# 引入本地目录 ,copy到镜像内部
ADD soft/ /opt/soft
# 初始化工作目录
WORKDIR /data/wwwroot
# Write Permission
# 权限操作
RUN usermod -u 1000 www-data
# 有问题求助搜索引擎,我也是小白,共勉
# 执行脚本
CMD ["/init.sh"]
# Expose ports.
# 目录映射|挂载
VOLUME ["/opt"]
# 端口开放
EXPOSE 9000 22
```
###根据dockerfile编译对应的images
```ssh
docker build -t image_name:tag dockerfile所在目录/
```
###使用docker-compose管理docker镜像和容器,配置文件docker-compose.yml
```yml
## 容器名称
web:
    ## 使用的镜像,如果本地不存在,会自动搜索docker镜像库
    image: ecstore/swoole5.6
    ## 端口映射,本地端口和docler内部端口对应关系
    ports:
      - "11080:80"
    ## 关联容器
    links:
      - "mysql"
    ## 本地目录挂载
    volumes:
      - ./public:/data/httpd/public

mysql:
    image: zsz/mysql-5.6
    ports:
      - "11306:3306"
    volumes:
      - ./mysql-data:/var/lib/mysql
    ## 环境变量
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: newecstore
      MYSQL_USER: ecstore
      MYSQL_PASSWORD: 123123
```
###常用命令
```ssh
# 查看本地镜像
$ docker images
# 删除镜像,强制删除-f参数,注意:删除镜像后,对应基于基础镜像的镜像或者容器也会出问题
$ docker rmi docker_image_id
# 帮助文档
$ docker help
# 删除镜像或者容器
$ docker rm docker_image_id
# 查看运行中的容器
$ docker ps
# 关闭运行中的容器
$ docker stop
# 启动容器
$ docker run
# 启动容器
$ docker start
# 进入容器
$ docker exec -it image_id /in/bash
# docker-compose 容器启动
$ docker-compose up -d
# docker-compose 容器重启
$ docker-compose restart
# docker-compose 容器停止
$ docker-compose stop
# docker-compose 容器停止并删除未提交内容
$ docker-compose down
```