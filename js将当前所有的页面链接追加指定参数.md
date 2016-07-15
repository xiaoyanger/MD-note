layout: '[layout]'
title:  js将当前所有的页面链接追加指定参数
date:  2016-05-01 23:16:05
tags: [js]
categories: [js]
---
# js将当前所有的页面链接追加指定参数

```js
window.onload =function (){
var cur_url = window.location.href. toString(); //获取当前页面的url
var cur_fix_index = cur_url .indexOf( '?');//
var level_index   = cur_url .indexOf( 'level');
var extend_id     = '';
if (level_index > 0){
extend_id     = cur_url. substring(level_index );
var url_arr = new Array ();
url_arr = document.getElementsByTagName ("a");
for (var j = 0 ; j < url_arr .length; j++ ){
if ( url_arr[j ].href != '' && url_arr [j]. href.indexOf ('javascript') < 0  && url_arr[j ].href. indexOf('#' ) < 0 ){//判断当前的链接是不是真的是一个有效的链接地址
                    if ( url_arr[j ].href. indexOf('?' ) < 0 ){//判断当前的a链接是不是有？号
if (url_arr [j]. href.indexOf ('level') < 0){ //判断当前的a链接是不是已经有level参数
url_arr[j ].href = url_arr [j]. href + '?' + extend_id;
                             }
                    } else {
if (url_arr [j]. href.indexOf ('level') < 0){ //判断当前的a链接是不是已经有level参数
url_arr[j ].href = url_arr [j]. href + '&' + extend_id;
                        }
                    }
                }
            }
    }
}
```

因为部分原因修改之后的方法

```sh
window.onload=function (){
    var cur_url = window.location.href.toString(); //获取当前页面的url
     //var cur_fix_index = cur_url.indexOf('?');//
    var level_index   = cur_url.indexOf('level');
    var pattern = new RegExp('level=[0-9]*');
    var extend_id     = '';
    if (level_index > 0){
        // extend_id     = cur_url.substring(level_index);
        extend_id     = cur_url.match(pattern);
            var url_arr = new Array();
            url_arr = document.getElementsByTagName("a");
            for (var j= 0 ; j < url_arr.length; j++){
                if (url_arr[j].href != '' && url_arr[j].href.indexOf('javascript') < 0  && url_arr[j].href.indexOf('#') < 0){//判断当前的链接是不是真的是一个有效的链接地址
                    if (url_arr[j].href.indexOf('?') < 0){//判断当前的a链接是不是有？号
                             if (url_arr[j].href.indexOf('level') < 0){//判断当前的a链接是不是已经有level参数
                                url_arr[j].href = url_arr[j].href + '?' + extend_id;
                             }
                    } else {
                        if (url_arr[j].href.indexOf('level') < 0){//判断当前的a链接是不是已经有level参数
                                url_arr[j].href = url_arr[j].href + '&' + extend_id;
                        }
                    }
                }
            }
    }
}
```