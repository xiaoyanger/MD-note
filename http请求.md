layout: '[layout]'
title: http请求
date: 2016-1-6 00:00:00
tags: [http]
---

请求头（消息头）包含（客户机请求的服务器主机名，客户机的环境信息等）：

```
    Accept：用于告诉服务器，客户机支持的数据类型 （例如：Accept:text/html,image/*）
    Accept-Charset：用于告诉服务器，客户机采用的编码格式
    Accept-Encoding：用于告诉服务器，客户机支持的数据压缩格式
    Accept-Language：客户机语言环境
    Host:客户机通过这个服务器，想访问的主机名
    If-Modified-Since：客户机通过这个头告诉服务器，资源的缓存时间
    Referer：客户机通过这个头告诉服务器，它（客户端）是从哪个资源来访问服务器的（防盗链）
    User-Agent：客户机通过这个头告诉服务器，客户机的软件环境（操作系统，浏览器版本等）
    Cookie：客户机通过这个头，将Coockie信息带给服务器
    Connection：告诉服务器，请求完成后，是否保持连接
    Date：告诉服务器，当前请求的时间
```

    一个http响应代表服务器端向客户端回送的数据，它包括：
    一个状态行，若干个响应消息头，以及实体内容
   
    状态行： 例如： HTTP/1.1 200 OK （协议的版本号是1.1 响应状态码为200 响应结果为 OK）
    响应头(消息头)包含:
``` 
    Location：这个头配合302状态吗，用于告诉客户端找谁
    Server：服务器通过这个头，告诉浏览器服务器的类型
    Content-Encoding：告诉浏览器，服务器的数据压缩格式
    Content-Length：告诉浏览器，回送数据的长度
    Content-Type：告诉浏览器，回送数据的类型
    Last-Modified：告诉浏览器当前资源缓存时间
    Refresh：告诉浏览器，隔多长时间刷新
    Content-Disposition：告诉浏览器以下载的方式打开数据。例如：
    context.Response.AddHeader("Content-Disposition","attachment:filename=icon.jpg");
    context.Response.WriteFile("icon.jpg"); 
```
```
    Transfer-Encoding：告诉浏览器，传送数据的编码格式
    ETag：缓存相关的头（可以做到实时更新）
    Expries：告诉浏览器回送的资源缓存多长时间。如果是-1或者0，表示不缓存
    Cache-Control：控制浏览器不要缓存数据 no-cache
    Pragma：控制浏览器不要缓存数据 no-cache
    Connection：响应完成后，是否断开连接。 close/Keep-Alive
    Date：告诉浏览器，服务器响应时间
```