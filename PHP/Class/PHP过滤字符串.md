layout: '[layout]'
title: PHP过滤字符串
date:  2016-06-01 07:19:15
tags: [php]
categories: [php]
---
---PHP过滤字符串---
  
# 方法一
```php
function strFilter($str){
$str = str_replace('`', '', $str );
$str = str_replace('·', '', $str );
$str = str_replace('~', '', $str );
$str = str_replace('!', '', $str );
$str = str_replace('！', '', $str );
$str = str_replace('@', '', $str );
$str = str_replace('#', '', $str );
$str = str_replace('$', '', $str );
$str = str_replace('￥', '', $str );
$str = str_replace('%', '', $str );
$str = str_replace('^', '', $str );
$str = str_replace('……', '', $str );
$str = str_replace('&', '', $str );
$str = str_replace('*', '', $str );
$str = str_replace('(', '', $str );
$str = str_replace(')', '', $str );
$str = str_replace('（', '', $str );
$str = str_replace('）', '', $str );
$str = str_replace('-', '', $str );
$str = str_replace('_', '', $str );
$str = str_replace('——', '', $str );
$str = str_replace('+', '', $str );
$str = str_replace('=', '', $str );
$str = str_replace('|', '', $str );
$str = str_replace('\\ ', '' , $str);
$str = str_replace('[', '', $str );
$str = str_replace(']', '', $str );
$str = str_replace('【', '', $str );
$str = str_replace('】', '', $str );
$str = str_replace('{', '', $str );
$str = str_replace('}', '', $str );
$str = str_replace(';', '', $str );
$str = str_replace('；', '', $str );
$str = str_replace(':', '', $str );
$str = str_replace('：', '', $str );
$str = str_replace('\' ', '' , $str);
$str = str_replace('"', '', $str );
$str = str_replace('“', '', $str );
$str = str_replace('”', '', $str );
$str = str_replace(',', '', $str );
$str = str_replace('，', '', $str );
$str = str_replace('<', '', $str );
$str = str_replace('>', '', $str );
$str = str_replace('《', '', $str );
$str = str_replace('》', '', $str );
$str = str_replace('.', '', $str );
$str = str_replace('。', '', $str );
$str = str_replace('/', '', $str );
$str = str_replace('、', '', $str );
$str = str_replace('?', '', $str );
$str = str_replace('？', '', $str );
return trim($str);
}
```
# 方法二
```php
function replace_specialChar($strParam){
$regex = "/\/| \~|\! |\@| \#|\\ $|\%| \^|\& |\*| \(|\) |\_| \+|\{ |\}| \:|\< |\>| \?|\[ |\]| \,|\. |\/| \;|\' |\`| \-|\= |\\\|\| /";
return preg_replace($regex, "",$strParam );
}
$str = "123~!@#$%^&*()_+{}:|<>?,./;'[]\-=`456" ;
$str = replace_specialChar($str );
echo $str;
```
 
# 方法三
```php
/*
* 过滤敏感字符
*/
public function replace_specialChar($strParam){
$regex = "/\/|\~|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\+|\{|\}|\:|\<|\>|\?|\[|\]|\,|\.|\/|\;|\'|\`|\-|\=|\\\|\|/";
return preg_replace($regex,"",$strParam);
}
```

# 儿童节快乐