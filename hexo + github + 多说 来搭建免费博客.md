layout: '[layout]'
title: hexo + github + 多说 来搭建免费博客
date: 2015-11-01 23:55:04
tags: [hexo,git,github,duoshuo]
categories: [hexo,github]
---

# 要用到的模块
hexo 是一款快速、简单、并且强大的博客博客模板框架 - 基于nodejs 。

github 如果你是程序员 那么github就不说了。不了解的可以点击这里github百度百科

多说 可以让你博客在没有动态服务器的情况下 ， 依然可以加入评论系统。

# 安装 hexo 生成blog
## 安装与简单发布

首先 , 我们要现在通过npm安装 hexo

```sh
$ npm install hexo -g
```
安装好了之后 我们就可以初始化一个blog 并且启动服务器了

```sh
$ hexo init blog
$ cd blog
$ npm install
$ hexo server
```

此时文件目录为
```
blog
_config_yml // 注配置文件
db.json // 数据
debug.log // 调试日志
_node_mudules // nodejs 相关依赖
package.json // 配置依赖
scaffolds // 脚手架 - 也就是一个工具模板
source // 存放blog正文的地方
themes // 存放皮肤的地方
```
默认访问 http://localhost:4000

## 页面打不开

如果你的电脑没有翻-墙 可能会打不开页面。因为页面中默认使用了ajax.google.com 下的js包。因此我们要把包删掉

解决办法：

进入你刚新建好的 blog根目录 ，进入


themes/landscape/layout/_partial
- 找到 after-footer.ejs

把

<script src="//ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js"> </script>

替换成

<script src="http://cdn.bootcss.com/jquery/2.1.1/jquery.min.js“ > </script>

- 找到 header.ejs

注释掉或者删掉 下面这句css引用

<link href="//fonts.googleapis.com/css?family=Source+Code+Pro" rel=”stylesheet” type=”text/css”>

重新 hexo server 之后。访问 http://localhost:4000 就会看到blog主页了。

- 新建文章

当我们想写一篇blog时 在blog根目录下我们可以通过 hexo new <title> 指令来实现

例如我们想新建一篇主题为hello的blog

```
hexo new hello
```
输出信息如下

```
[info] File created at /Users/Night/Web/projects/java/temp/blog-test/source/_posts/hello.md
```
hexo会在 source/_posts/ 下新建hello.md 文件。

编辑 hello.md 就是编辑你的blog内容了 — markdown语法

hello.md 的文档和目录可以这样添加
 ```
title: hexo、github、多说、搭建免费博客
date: 2014-10-19 12:56:58
tags:
- tag1
- tag2
- tag3
categories:
- 目录
__

// 你的内容
<!--more--> // 以上为摘要
```
##  静态处理

因为我们的blog要部署到github静态服务器上面，所有我们还要将页面进行静态化

hexo 为我们提供了 hexo g 的方法。进入blog根目录 执行

```
$ hexo g
```
会有如下提示信息

```
[info] Files loaded in 0.081s
[create] Generated: archives/index.html (48ms)
[create] Generated: archives/2014/index.html (10ms)
[create] Generated: archives/2014/10/index.html (7ms)
[create] Generated: index.html (9ms)
[create] Generated: 2014/10/19/hello-world/index.html (19ms)
[create] Generated: js/script.js (5ms)
[create] Generated: css/style.css (434ms)
[create] Generated: css/fonts/FontAwesome.otf (1ms)
[create] Generated: css/fonts/fontawesome-webfont.eot (2ms)
[create] Generated: css/fonts/fontawesome-webfont.svg (2ms)
[create] Generated: css/fonts/fontawesome-webfont.ttf (4ms)
[create] Generated: css/fonts/fontawesome-webfont.woff (1ms)
[create] Generated: css/images/banner.jpg (3ms)
[create] Generated: fancybox/blank.gif (2ms)
[create] Generated: fancybox/fancybox_loading.gif (1ms)
[create] Generated: fancybox/fancybox_loading@2x.gif (1ms)
[create] Generated: fancybox/fancybox_overlay.png (1ms)
[create] Generated: fancybox/fancybox_sprite.png (0ms)
[create] Generated: fancybox/fancybox_sprite@2x.png (1ms)
[create] Generated: fancybox/jquery.fancybox.css (0ms)
[create] Generated: fancybox/jquery.fancybox.js (1ms)
[create] Generated: fancybox/jquery.fancybox.pack.js (1ms)
[create] Generated: fancybox/helpers/fancybox_buttons.png (1ms)
[create] Generated: fancybox/helpers/jquery.fancybox-buttons.css (1ms)
[create] Generated: fancybox/helpers/jquery.fancybox-buttons.js (0ms)
[create] Generated: fancybox/helpers/jquery.fancybox-media.js (1ms)
[create] Generated: fancybox/helpers/jquery.fancybox-thumbs.css (2ms)
[create] Generated: fancybox/helpers/jquery.fancybox-thumbs.js (1ms)
[info] 28 files generated in 0.565s
```
blog根目录下会生成public文件夹-里面就是刚才生成的静态文件

hexo 的详细实用说明请参看官方文档

# 将blog部署到 github
## 部署到github

部署到github 非常简单。因为hexo已经为你集成好了发布到github的配置。

我们只需要 修改 blog 目录下的 _config.yml 文件

打开 _config.yml 找到如下配置

```
deploy:
  type: github
  repo: https://github.com/yourname/blog.git
```
修改 repo : ‘your github repo’

回到 blog 目录 执行

```
hexo deploy
```
你会发现public 目录下的页面已经发布到github gh-pages 分支了

往后我们要做的就是用自己的域名指向 github。

## 域名指向

### 主域名指向

例如 我的域名为 netpi.me

- 我们要在github blog项目下新建一个CNAME文件 内容为 netpi.me

- 将我们得域名A 记录到 207.97.227.245 这个ip

域名解析完成后我们 访问netpi.me 就是我们的blog了

### 子域名指向

例如 我的域名为 blog.netpi.me

- 我们要在github blog项目下新建一个CNAME文件 内容为 blog.netpi.me

- 将我们得域名CNAME 到 ‘你的github名’.github.io (例如 netpi.github.io)

域名解析完成后我们 访问blog.netpi.me 就是我们的blog了

解析期间我们可以用

```
ping netpi.me
```
来方便的获知 解析情况。

# 插件安装
## 多说

评论插件多说为 hexo量身打造了一套解决方案

Hexo使用多说教程

站点创建地址

## rss

rss的安装非常简单 ，已经有人做好了插件，安装只需一步。

```
npm install hexo-generator-feed
```
启动服务器，用浏览器打开 http://localhost:4000/atom.xml， 就可以看到RSS已经生效了。

## sitemap

同样是一条命令，就可以完成。

```
npm install hexo-generator-sitemap
```
启动服务器，用浏览器打开 用浏览器打开 http://localhost:4000/sitemap.xml 发现site已经生效

* License
注 ：转载 请注明出处 http://blog.netpi.me/实用/hexo
