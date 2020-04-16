layout: '[layout]'
title: PHP生成Excel的方法
date: 2015-11-5 00:00:00
tags: [php,excel]
---
PHP生成Excel的方法

    <?php
        public static function SendExcel($filename = '表格007',$title=array(),$content = array()){
            if($title){
                $result = implode(",",$title)."\r\n";
            }
            foreach ($content as $key => $value) {
                $result .= implode(',',$value)."\r\n";
            }
            $encode = mb_detect_encoding($result, array("ASCII",'UTF-8',"GB2312","GBK",'BIG5'));//检测当前的编码格式
            $result = mb_convert_encoding($result, 'GB2312', $encode);//转换编码格式为制定的格式
            header("Content-type:application/vnd.ms-excel;charset=gb2312");
            header('Content-Type: applicationnd.ms-excel;');
            header('Content-Disposition: attachment;filename="'.$filename.'.csv"');
            header('Cache-Control: max-age=0');
            header('Cache-Control: max-age=1');
            header ('Expires: Mon, 26 Jul 1997 05:00:00 GMT');
            header ('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT');
            header ('Cache-Control: cache, must-revalidate');
            header ('Pragma: public');
            header("Content-Type: application/force-download");
            echo $result;
        }
    ?>