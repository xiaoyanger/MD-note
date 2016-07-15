layout: '[layout]'
title: contos安装laravel
date: 2015-11-8 00:00:00
tags: [laravel,框架,linux,composer]
categories: [技术,教程]
---
## 安装composer脚本：

```sh
    $ curl –sS https://getcomposer.org/installer | php
    - All settings correct for using Composer
    - Downloading...
    - Composer successfully installed to: /home/tools/composer.phar
    - Use it: php composer.phar
    $ mv composer.phar /usr/bin/composer
```

## 安装laravel
通过 Laravel 安装器安装首先，通过 Composer 下载 Laravel 安装器。

```sh
    composer global require "laravel/installer=~1.1"
```

请确保把 ~/.composer/vendor/bin 路径添加到 PATH 环境变量里, 这样laravel 可执行文件才能被命令行找到, 以后您就可以在命令行下直接使用 laravel 命令.
安装成功后, 可以使用命令 laravel new 在您指定的目录下创建一份全新安装的 Laravel。例如，laravel new blog 将会在当前目录下创建一个叫 blog 的目录, 此目录里面存放着全新安装的 Laravel 以及其依赖的工具包。这种安装方法比通过 Composer 安装要快许多。
通过 Composer 的 create-project 命令安装 Laravel还可以通过在命令行执行 Composer 的 create-project 命令来安装Laravel：

```sh
composer create-project laravel/laravel --prefer-dist
```

通过下载 Laravel 包安装
http://down.golaravel.com/laravel/laravel-master.zip（由于国内被墙的厉害，所以这里有初始化好的项目下载）
Composer 安装完成后，下载最新版Laravel框架，把它解压缩到你服务器上的一个目录中。然后在 Laravel 应用的根目录下运行命令行命令 php composer.phar install （或者 composer install ）来安装所有的框架依赖包。在此过程中，为了成功完成安装，你需要在服务器上安装好 Git。

当 Laravel 框架安装好后，你可以使用命令行命令 php composer.phar update 来更新框架。



执行composer的时候同样遇到被墙的问题，所以修改下源吧

```
/root/.composer/config.json 
          
        {
            "config": {},
            "repositories": [
                {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
                {"packagist": false}
            ]
        }
```

或者

```
    {
        "config": {},
        "repositories": [
            {
                "type": "composer",
                "url": "http://comproxy.cn/repo/packagist"
            }, {
                "packagist": false
            }
        ]
    }
```

```sh
    Warning: require(/home/www/laravel/bootstrap/../vendor/autoload.php): failed to open stream: No such file or directory in /home/www/laravel/bootstrap/autoload.php on line 17

    Fatal error: require(): Failed opening required '/home/www/laravel/bootstrap/../vendor/autoload.php' (include_path='.:/usr/local/php/lib/php') in /home/www/laravel/bootstrap/autoload.php on line 17'
```

解决：
    如果是已经有了 vendor扩展包，执行 composer update，没有就执行composer install
## 修改文件夹权限，让网站的www用户拥有读写执行权限【网站出现白板，多半是因为没有权限】

```sh
    #chown -R www.www laravel               #修改所属用户和用户组
    #chmod -R 755 laravel                        #修改权限
```

   然后打开Debug模式
        在app/config/app.php中将’debug’=>false 改为’debug’=>true
