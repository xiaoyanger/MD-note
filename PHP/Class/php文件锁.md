layout: '[layout]'
title: php文件锁
date: 2016-04-12 23:55:04
tags: [php]
categories: [php]
---
## 原理

临时创建一个文件
当检测到这个文件是存在的，并且时间不是到达正确的时间，直接返回不继续
检测到文件不存在或者创建的时间在规定时间之前，则重新将文件创建一次

## code
```php
const DEBUG = false;
// 检验脚本是否正在执行
$lock_file = __DIR__ . '/appointment_out_date_lock';
// 锁文件存在并且创建时间小于5Min
if (file_exists($lock_file) && ! (time() > filemtime($lock_file) + 5 * 60)) {
    if (DEBUG) {
        echo "Into lock file.\n\n";
    }
    return 1;
}
// 创建文件锁或更新锁文件的最后修改时间为当前时间
touch($lock_file);
```