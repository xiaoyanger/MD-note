layout: '[layout]'
title: php-zip文件简单操作
date: 2016-03-04 18:46:05
tags: [php,zip]
categories: [php,zip]
---
### php-zip文件

```php
<?php
$zip = new ZipArchive();
$filename='thisisa.zip';
$path = '/tmp/www/file/';
 !is_dir($path) AND mkdir($path, 0777, true);
 !file_exists(filename)  AND mkdir($filename,0777);

if ($zip->open($filename, ZIPARCHIVE::CREATE)!==TRUE) {
  exit("cannot open <$filename>\n");
}
addfile = 'aa.excel';
$zip->addFile($v,$addfile);
