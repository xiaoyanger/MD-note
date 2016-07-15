layout: '[layout]'
title: PHP操作head和浏览器的小方法
date:  2016-06-07 07:21:15
tags: [php]
categories: [php]
---
--- PHP操作head和浏览器的小方法 ---
  

# CODE1-获取浏览器
```php
function get__browser()
{
    // 默认为 chrome 标准浏览器
    $browser = array(
        'device' => 'pc', // pc|mobile|pad
        'name' => 'chrome', // chrome|firefox|ie|opera
        'version' => 30,
    );
    $agent = isset($_SERVER['HTTP_USER_AGENT']) ? strtolower($_SERVER['HTTP_USER_AGENT']) : '';
    // 主要判断是否为垃圾IE6789
    if (strpos($agent, 'msie') !== FALSE || stripos($agent, 'trident') !== FALSE) {
        $browser['name'] = 'ie';
        $browser['version'] = 8;
        preg_match('#MSIE\s*([\d\.]+)#is', $agent, $m);
        if (!empty($m[1])) {
            $v = intval($m[1]);
            $browser['version'] = $v;
        } else {
            // 匹配兼容模式 Trident/7.0，兼容模式下会有此标志 $trident = 7;
            preg_match('#Trident/([\d\.]+)#is', $agent, $m);
            if (!empty($m[1])) {
                $trident = intval($m[1]);
                $trident == 4 AND $browser['version'] = 8;
                $trident == 5 AND $browser['version'] = 9;
                $trident > 5 AND $browser['version'] = 10;
            }
        }
    }

    if (isset($_SERVER['HTTP_X_WAP_PROFILE']) || (isset($_SERVER['HTTP_VIA']) && stristr($_SERVER['HTTP_VIA'], "wap") || stripos($agent, 'phone') || strpos($agent, 'ipod'))) {
        $browser['device'] = 'phone';
    } elseif (strpos($agent, 'pad')) {
        $browser['device'] = 'pad';
    } else {
        $robots = array('bot', 'spider', 'slurp');
        foreach ($robots as $robot) {
            if (strpos($agent, $robot) !== FALSE) {
                $browser['name'] = 'robot';
                return;
            }
        }
    }
    return $browser;
}
```

# CODE2-获取head请求头
```php
function get_head($sUrl ){
$oCurl = curl_init ();
// 设置请求头, 有时候需要,有时候不用,看请求网址是否有对应的要求
$header [] = "Content-type: application/x-www-form-urlencoded";
$user_agent = "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/33.0.1750.146 Safari/537.36";

curl_setopt( $oCurl, CURLOPT_URL, $sUrl );
curl_setopt( $oCurl, CURLOPT_HTTPHEADER ,$header );
// 返回 response_header, 该选项非常重要,如果不为 true, 只会获得响应的正文
curl_setopt ($oCurl , CURLOPT_HEADER, true);
// 是否不需要响应的正文,为了节省带宽及时间,在只需要响应头的情况下可以不要正文
curl_setopt ($oCurl , CURLOPT_NOBODY, true);
// 使用上面定义的 ua
curl_setopt ($oCurl , CURLOPT_USERAGENT,$user_agent);
curl_setopt( $oCurl, CURLOPT_RETURNTRANSFER , 1 );
// 不用 POST 方式请求, 意思就是通过 GET 请求
curl_setopt ($oCurl , CURLOPT_POST, false);

$sContent = curl_exec ($oCurl );
// 获得响应结果里的：头大小
$headerSize = curl_getinfo ($oCurl , CURLINFO_HEADER_SIZE);
// 根据头大小去获取头信息内容
$header = substr ($sContent , 0 , $headerSize );

curl_close( $oCurl);

return $header;
}
```
# CODE3-获取分页
```php
function curPageURL()
{
$pageURL = 'http';
if ($_SERVER ["HTTPS"] == "on")
    {
$pageURL .= "s";
    }
$pageURL .= "://";
if ($_SERVER ["SERVER_PORT"] != "80")
    {
$pageURL .= $_SERVER ["SERVER_NAME"] . ":" . $_SERVER["SERVER_PORT"] . $_SERVER[ "REQUEST_URI"];
    }
else
{
$pageURL .= $_SERVER ["SERVER_NAME"] . $_SERVER["REQUEST_URI"];
    }
return $pageURL;
}
function getallheaders()
{
$headers = '';
foreach ($_SERVER as $name => $value)
    {
if (substr($name , 0 , 5 ) == 'HTTP_')
        {
$headers [str_replace (' ', '-', ucwords(strtolower(str_replace('_', ' ', substr( $name, 5)))))] = $value;
        }
    }
return $headers;
}
```
# CODE2-处理head请求头
```php
function parseHeaders( $headers )
{
$head = array();
foreach ( $headers as $k=> $v )
    {
$t = explode ( ':', $v, 2 );
if( isset( $t[ 1] ) )
$head [ trim ($t [0 ]) ] = trim( $t [1 ] );
else
{
$head [] = $v ;
if( preg_match( "#HTTP/[0-9\.]+\s +([0-9]+)#",$v , $out ) )
$head[ 'reponse_code'] = intval ($out [1 ]);
        }
    }
return $head;
}
```