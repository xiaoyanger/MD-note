layout: '[layout]'
title: PHP-log日志类
date: 2016-02-20 00:00:00
tags:  [php,log]
---
PHP-log日志类

```php
    <?php
    
    if(!isset($_SERVER['time'])) {
        $_SERVER['time'] = time();
        $_SERVER['time_fmt'] = gmdate('y-n-j H:i', time() + 8 * 3600);
    }
    
    class log {
        public static function write($s, $file = 'phperror.php') {
            $s = self::safe_str($s);
            $ip = $_SERVER['ip'];
            $time = date('Y-n-d H:i', $_SERVER['time']);
            $url = $_SERVER['REQUEST_URI'];
            $url = self::safe_str($url);
            $s = '<?php exit;?>'."     $time     $ip     $url     $s     \r\n";
            self::real_write($s, $file);
            return TRUE;
        }
    
        /**
         * 日志
         */
        public static function writelog($file, $log) {
           // global $_G;
            //$yearmonth = dgmdate(TIMESTAMP, 'Ym', $_G['setting']['timeoffset']);
            $yearmonth = date('Ym', time());
            $logfile = LOG_PATH.$yearmonth.'_'.$file.'.php';
            if(@filesize($logfile) > 2048000) {
                $dir = opendir(LOG_PATH);
                $length = strlen($file);
                $maxid = $id = 0;
                while($entry = readdir($dir)) {
                    if(strpos($entry, $yearmonth.'_'.$file) !== false) {
                        $id = intval(substr($entry, $length + 8, -4));
                        $id > $maxid && $maxid = $id;
                    }
                }
                closedir($dir);
    
                $logfilebak = LOG_PATH.$yearmonth.'_'.$file.'_'.($maxid + 1).'.php';
                @rename($logfile, $logfilebak);
            }
            if($fp = @fopen($logfile, 'a')) {
                @flock($fp, 2);
                if(!is_array($log)) {
                    $log = array($log);
                }
                foreach($log as $tmp) {
                    fwrite($fp, "<?PHP exit;?>\t".str_replace(array('<?', '?>'), '', $tmp)."\n");
                }
                fclose($fp);
            }
        }
    
        /**
         * 错误日志
         */
        public static function write_error_log($message) {
    
            $message = discuz_error::clear($message);
            $time = time();
            $file =  DISCUZ_ROOT.'./data/log/'.date("Ym").'_errorlog.php';
            $hash = md5($message);
    
            $uid = getglobal('uid');
            $ip = getglobal('clientip');
    
            $user = '<b>User:</b> uid='.intval($uid).'; IP='.$ip.'; RIP:'.$_SERVER['REMOTE_ADDR'];
            $uri = 'Request: '.dhtmlspecialchars(discuz_error::clear($_SERVER['REQUEST_URI']));
            $message = "<?PHP exit;?>\t{$time}\t$message\t$hash\t$user $uri\n";
            if($fp = @fopen($file, 'rb')) {
                $lastlen = 50000;
                $maxtime = 60 * 10;
                $offset = filesize($file) - $lastlen;
                if($offset > 0) {
                    fseek($fp, $offset);
                }
                if($data = fread($fp, $lastlen)) {
                    $array = explode("\n", $data);
                    if(is_array($array)) foreach($array as $key => $val) {
                        $row = explode("\t", $val);
                        if($row[0] != '<?PHP exit;?>') continue;
                        if($row[3] == $hash && ($row[1] > $time - $maxtime)) {
                            return;
                        }
                    }
                }
            }
            error_log($message, 3, $file);
        }
    
        /**
         * 数据库错误
         */
        public static function db_error($message, $sql) {
            global $_G;
    
            list($showtrace, $logtrace) = discuz_error::debug_backtrace();
    
            $title = lang('error', 'db_'.$message);
            $title_msg = lang('error', 'db_error_message');
            $title_sql = lang('error', 'db_query_sql');
            $title_backtrace = lang('error', 'backtrace');
            $title_help = lang('error', 'db_help_link');
    
            $db = &DB::object();
            $dberrno = $db->errno();
            $dberror = str_replace($db->tablepre,  '', $db->error());
            $sql = dhtmlspecialchars(str_replace($db->tablepre,  '', $sql));
    
            $msg = '<li>[Type] '.$title.'</li>';
            $msg .= $dberrno ? '<li>['.$dberrno.'] '.$dberror.'</li>' : '';
            $msg .= $sql ? '<li>[Query] '.$sql.'</li>' : '';
    
            discuz_error::show_error('db', $msg, $showtrace, false);
            unset($msg, $phperror);
    
            $errormsg = '<b>'.$title.'</b>';
            $errormsg .= "[$dberrno]<br /><b>ERR:</b> $dberror<br />";
            if($sql) {
                $errormsg .= '<b>SQL:</b> '.$sql;
            }
            $errormsg .= "<br />";
            $errormsg .= '<b>PHP:</b> '.$logtrace;
    
            discuz_error::write_error_log($errormsg);
            exit();
    
        }
    
        /**
         * 异常错误
         */
        public static function exception_error($exception) {
    
            if($exception instanceof DbException) {
                $type = 'db';
            } else {
                $type = 'system';
            }
    
            if($type == 'db') {
                $errormsg = '('.$exception->getCode().') ';
                $errormsg .= self::sql_clear($exception->getMessage());
                if($exception->getSql()) {
                    $errormsg .= '<div class="sql">';
                    $errormsg .= self::sql_clear($exception->getSql());
                    $errormsg .= '</div>';
                }
            } else {
                $errormsg = $exception->getMessage();
            }
    
            $trace = $exception->getTrace();
            krsort($trace);
    
            $trace[] = array('file'=>$exception->getFile(), 'line'=>$exception->getLine(), 'function'=> 'break');
            $phpmsg = array();
            foreach ($trace as $error) {
                if(!empty($error['function'])) {
                    $fun = '';
                    if(!empty($error['class'])) {
                        $fun .= $error['class'].$error['type'];
                    }
                    $fun .= $error['function'].'(';
                    if(!empty($error['args'])) {
                        $mark = '';
                        foreach($error['args'] as $arg) {
                            $fun .= $mark;
                            if(is_array($arg)) {
                                $fun .= 'Array';
                            } elseif(is_bool($arg)) {
                                $fun .= $arg ? 'true' : 'false';
                            } elseif(is_int($arg)) {
                                $fun .= (defined('DISCUZ_DEBUG') && DISCUZ_DEBUG) ? $arg : '%d';
                            } elseif(is_float($arg)) {
                                $fun .= (defined('DISCUZ_DEBUG') && DISCUZ_DEBUG) ? $arg : '%f';
                            } else {
                                $fun .= (defined('DISCUZ_DEBUG') && DISCUZ_DEBUG) ? '\''.dhtmlspecialchars(substr(self::clear($arg), 0, 10)).(strlen($arg) > 10 ? ' ...' : '').'\'' : '%s';
                            }
                            $mark = ', ';
                        }
                    }
    
                    $fun .= ')';
                    $error['function'] = $fun;
                }
                $phpmsg[] = array(
                    'file' => str_replace(array(DISCUZ_ROOT, '\\'), array('', '/'), $error['file']),
                    'line' => $error['line'],
                    'function' => $error['function'],
                );
            }
    
            self::show_error($type, $errormsg, $phpmsg);
            exit();
    
        }
        // 直接写入
        public static function real_write($s, $file = 'phperror.php') {
    
            $logpath = LOG_PATH;
            $logfile = $logpath.$file;
            try {
                $fp = fopen($logfile, 'ab+');
                if(!$fp) {
                    throw new Exception('写入日志失败，可能磁盘已满，或者文件'.$logfile.'不可写。');
                }
                fwrite($fp, $s);
                fclose($fp);
            } catch (Exception $e) {}
            return TRUE;
        }
    
        public static function safe_str($s) {
            $s = str_replace("\r\n", ' ', $s);
            $s = str_replace("\r", ' ', $s);
            $s = str_replace("\n", ' ', $s);
            $s = str_replace("\t", ' ', $s);
            return $s;
        }
    
        // 跟踪变量的值
        public static function trace($s) {
            if(!DEBUG) return;
            $processtime = number_format(microtime(1) - $_SERVER['time'], 3, '.', '');
            empty($_SERVER['trace']) && $_SERVER['trace'] = '';
            $_SERVER['trace'] .= "$s - $processtime\r\n";
        }
    
        // 保存 trace
        public static function trace_save() {
            $s = "\r\n\r\n---------------------------------------------------------------------------------\r\n<?php exit;?>\r\n---------------------------------------------------------------------------------\r\n$_SERVER[REQUEST_URI]\r\nPOST:".print_r($_POST, 1)."\r\nSQL:".print_r($_SERVER['sqls'], 1)."\r\n";
            $s .= $_SERVER['trace'];
            self::real_write($s, 'trace.php');
        }
    }
    ?>
```