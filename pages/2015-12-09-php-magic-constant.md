---
layout: post
title: PHP 的csv导出
category: PHP
tags: [PHP]
description: None
---

目前，大部分php的excel导出功能可能使用的都是php-excel

laravel的Excel也是基于php-excel的。

实际项目开发中，当数据量比较大的时候使用excel进行导出就变成一件很麻烦的事情。实际测试，当数据量达到7W+的时候就会出现失败的情况。这个时候可以考虑使用csv进行数据导出。

csv格式本质只是文本文件，相对于excel来说更加轻量、高效。而且使用excel打开效果可能并不会差太多。

目前遇到的csv导出最大的问题就是字符集。windows下默认是gbk，而mac下则是utf-8。因此实际的导出还需要提供两种数据格式化。



鉴于业务需要，就自己动手写了一个导出类。

# 具体代码

```php
<?php

/**
 * Class ExcelUtils
 * excel 工具类
 */
class ExcelUtils{

    // 获得一年内的月份，
    static public function getMonths(){
        $months= [];
        $now = strtotime(date('Y-m'));
      
        // 一年十二月的月份
        for ($i = 0; $i<12; $i++){
            $month_obj = new \stdClass();
            $month_obj->yearMonth = date('Y-m',strtotime("-{$i} months",$now));
            $months[] = $month_obj;
        }
        return $months;
    }

    // 获得自定义时间到当前时间的月份
    static public function getFreeMonths($time){
        // 检验日期格式有效性
        $isValid = strtotime($time);
        if (!$isValid) return false;

        $month = date('Y-m',strtotime("+1 month"));
        // 如果大于当前月，返回null
        if (strtotime($time) > strtotime($month)) return null;

        $months = [];
        $now = strtotime(date('Y-m'));
        for ($i = 0; $i < 12; $i++){
            $month_obj = new \stdClass();
            $month_obj->yearMonth = date('Y-m',strtotime("-{$i} month",$now));
            $months[] = $month_obj;

            if (strtotime($month_obj->yearMonth) < strtotime($time)) return $months;
        }
    }

    // 对要生成的excel 的数组的数据格式进行处理
    static public function getCorrectData($data){
        if (!is_array($data)) return false; // 如果不是数组，返回false;

        $result = [];
        foreach ($data as $v){
            if (!is_array($v)) return false; // 如果不是数组，返回false;
            $row = [];
            foreach ($v as $value){
                if (!is_scalar($value) && $value !== null) return false;

                // 如果单元格第一位是数字，在单元格末尾加入制表符，以保证excel打开不会格式化该单元格
                $first = substr($value,0,1);
                $count = strlen($value);
                if (is_numeric($value) && $count >9){
                    $column = "\t" . $value;
                }elseif(is_numeric($first)  && $count > 9) {
                    $column = "\t".$value;
                }else {
                    $column = $value;
                }

                $row[] = $column;
            }
            $result[] = $row;
        }

        if(!$result) return [];

        return $result;
    }

    // 生成csv 下载，gbk
    static public function downloadGbkCsv($title,$data){
        header('Content-Type:  application/vnd.ms-excel');
        header("Content-Disposition: attachment;filename={$title}.csv");
        header('Cache-Control: max-age=0');
        header('Cache-Control: max-age=1');
        header("Accept-Ranges:bytes");
        header ('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); // Date in the past
        header ('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT'); // always modified
        header ('Cache-Control: cache, must-revalidate'); // HTTP/1.1
        header ('Pragma: public'); // HTTP/1.0

        ob_flush();
        flush();

        //创建临时存储内存
        $fp = fopen('php://memory','w');
//        fwrite($fp, chr(0xEF).chr(0xBB).chr(0xBF));
        // 写入数据
        foreach($data as $v) {
            fputcsv($fp,$v,',');
        }
        rewind($fp);

        $content = "";
        while(!feof($fp)){
            $content .= fread($fp,1024);
        }
        fclose($fp);

        $content = mb_convert_encoding($content,'gbk','utf-8');  // 输出gbk 字符集，简体中文windows下默认字符集
        exit($content);
    }

    // utf-8+bom
    static public function downExcel($title,$data){
//        $title = mb_convert_encoding($title,'utf-8','gbk');
        header('Content-Type:  application/vnd.ms-excel');
        header("Content-Disposition: attachment;filename={$title}.csv");
        header('Cache-Control: max-age=0');
        header('Cache-Control: max-age=1');
        header("Accept-Ranges:bytes");
        header ('Expires: Mon, 26 Jul 1997 05:00:00 GMT'); // Date in the past
        header ('Last-Modified: '.gmdate('D, d M Y H:i:s').' GMT'); // always modified
        header ('Cache-Control: cache, must-revalidate'); // HTTP/1.1
        header ('Pragma: public'); // HTTP/1.0

        ob_flush();
        flush();

        //创建临时存储内存
        $fp = fopen('php://memory','w');
        fwrite($fp, chr(0xEF).chr(0xBB).chr(0xBF)); // 写入bom头
        // 写入数据
        foreach($data as $v) {
            fputcsv($fp,$v,',');
        }
        rewind($fp);

        $content = "";
        while(!feof($fp)){
            $content .= fread($fp,1024);
        }
        fclose($fp);

//        $content = mb_convert_encoding($content,'utf-8','gbk'); // 输出utf-8 格式数据
        exit($content);
    }
}

```

### 实际调用只需要：

    $title = "{$export_time}业绩统计";
    // 处理数据格式
    $excelData = ExcelUtils::getCorrectData($excelData);
    // 下载
    if ($excelData) {
    	if ($version == 2003) {
    		ExcelUtils::downExcel($title, $excelData);
    	} else {
             ExcelUtils::downloadGbkCsv($title, $excelData);
    	}
    }

# 总结

当然，这种实现方式只是很简单的一个类的调用。

实际的使用中发现，这种方式依旧不是很理想。主要问题出在各种各样的客户使用csv格式时的各种情况。其次就是请求时间过长，需要客户等待的时候可能会长一点。

**--END**