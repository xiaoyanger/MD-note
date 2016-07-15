layout: '[layout]'
title: 使用charles proxy for Mac来抓取手机App的网络包
date: 2015-11-7 00:00:00
tags: [抓包]
---
# 使用charles proxy for Mac来抓取手机App的网络包

1. 保证手机和Mac连接的是同一个无线局域网

2. 在Mac上打开Charles Proxy，菜单栏操作：Proxy→Proxy Setting，检查端口，一般保持默认，HTTP Proxy端口为8888

3. 查看Mac当前的IP地址，之后作为手机的Proxy IP用

4. 编辑手机的WIFI连接，长按当前连接的WIFI网络

5. 选择“修改网络”，进入网络连接的编辑模式（选择“显示高级选项”）并保存

6. 在手机App上随意进行一个操作

# 这种可以抓HTTP包了，如果需要抓HTTPS包的话

- 下载Charles证书http://www.charlesproxy.com/ssl.zip，解压后导入到iOS设备中（将crt文件作为邮件附件发给自己，再在iOS设备中点击附件即可安装）
- 在Charles的工具栏上点击设置按钮，选择Proxy Settings…
- 切换到SSL选项卡，选中Enable SSL Proxying，别急，选完先别关掉，还有下一步
- 在上一步的SSL选项卡的Locations表单填写要抓包的域名和端口，点击Add按钮，在弹出的表单中Host填写域名，比如填api.instagram.com，Port填443

其它:

1、如果没有设置ssl的话，那么你看到的ssl的接口中，会有一个报错:SSL Proxying not enabled for this host: enable in Proxy Settings, SSL locations，这个时候，在左侧的域名上点右键：enable ssl，就OK了。（其实就是上面的第4部，不过这个操作是简化操作，上面的第4步太麻烦了）

2、如果这样还是不能抓包，那么官方有说明（http://www.charlesproxy.com/documentation/using-charles/ssl-certificates/）