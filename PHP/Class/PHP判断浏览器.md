layout: '[layout]'
title: PHP判断浏览器
date: 2016-02-15 00:00:00
tags:  [php]
---
判断浏览器

```php
    <?php
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
    ?>
```