---
layout: post
title: PHP的魔术常量
category: PHP
tags: [PHP]
description: None
---

> 所谓魔术常量，其首先是一个常量，在常量的基础上又有一些自己的性质，那就是魔术常量会随着其在代码中的位置会表示不同的取值，这也就体现了“魔术”二字的含义。

众所周知，PHP是C-like的解释语言，其继承了C的大部分基础语法，魔术常量也继承了C语言的__FILE__和__LINE__，那么除此之外还做了一些扩充。截至目前的PHP 7.0版本，总共有8个魔术常量，在[官方网站](http://php.net/manual/zh/language.constants.predefined.php)有详细的介绍。

本文主要是针对各魔术常量做一个小小的Demo，以了解各常量的类型和含义。文中代码在PHP 5.6.15版本测试通过，版本过低可能不支持部分魔术常量的使用。

# Demo代码

```php
<?php

// 8个魔术常量(不区分大小写)

namespace NAMESPACE_NAME;
/**
 * __FILE__ : 文件名称, 字符串类型
 */
echo '__FILE__: ';
var_dump(__file__); 
echo "</br>";
echo "</br>";


/**
 * __LINE__ : 行号, 整型
 */
echo '__LINE__: ';
var_dump(__LINE__); 
echo "</br>";
echo "</br>";


/**
 * __DIR__ : 目录, 字符串类型
 * 注: 返回结果不包含结尾的"/"(根目录除外)
 */
echo '__DIR__: ';
var_dump(__DIR__); 
echo "</br>";
echo "</br>";


/**
 * __FUNCTION__ : 函数名称，字符串类型
 * 注:  1. 全局函数会
 */
function showFunction() {
    echo "__FUNCTIN__: ";
    var_dump(__FUNCTION__);
}
showFunction();
echo "</br>";
echo "</br>";


/**
 * __CLASS__ : 类名称，字符串类型
 * __METHOD__ : 方法名称，字符串类型
 * 注意__METHOD__和__FUNCTION__的区别: 方法名称包含类名称, 函数名称不包含类名称
 */
class BaseClass {
    function showClass() {
        echo "__CLASS__: ";
        var_dump(__CLASS__);
        echo "</br>";
    }
    function showFunction() {
        echo "&nbsp;&nbsp;__FUNCTION__ in class: ";
        var_dump(__FUNCTION__);
        echo "</br>";
    }
    function showMethod() {
        echo "&nbsp;&nbsp;__METHOD__ in class: ";
        var_dump(__METHOD__);
        echo "</br>";
    }
}
$obj = new BaseClass();
$obj->showClass();
$obj->showFunction();
$obj->showMethod();
echo "</br>";


/**
 * __TRAIT__ : Traits名称, 字符串类型，从PHP5.4.0开始支持
 */
trait HelloWorld {
    public function traitName() {
        echo __TRAIT__;
        //echo "__TRAIT__";
    }
} 
class A {
    use HelloWorld;
}
echo '__TRAIT__: ';
$obj = new A();
$obj->traitName();
echo "</br>";
echo "</br>";


/**
 * __NAMESPACE__ : 命名空间名称，字符串类型
 */
echo '__NAMESPACE__: ';
var_dump(__NAMESPACE__); 
echo "</br>";
echo "</br>";

?>
```

# 输出结果

    __FILE__: string(40) "/usr/local/nginx/html/magic_constant.php" 

    __LINE__: int(19) 

    __DIR__: string(21) "/usr/local/nginx/html" 

    __FUNCTIN__: string(27) "NAMESPACE_NAME\showFunction" 

    __CLASS__: string(24) "NAMESPACE_NAME\BaseClass" 
      __FUNCTION__ in class: string(12) "showFunction" 
      __METHOD__ in class: string(36) "NAMESPACE_NAME\BaseClass::showMethod" 

    __TRAIT__: NAMESPACE_NAME\HelloWorld

    __NAMESPACE__: string(14) "NAMESPACE_NAME" 


# 总结

1. **__FILE__**，__LINE__，__NAMESPACE__：这几个就是简单的字符串或者整数，直接使用即可；
2. **__DIR__**：注意其结尾没有“/”，但对于根目录除外，根目录的__DIR__是“/”；
3. **__FUNCION__**：
*.在没有namespace的情况下，全局函数和类成员函数的__FUNCTION__都是函数名称字符串“FUNCTION_NAME”；
*.在有namespace的情况下，全局函数的__FUNCTION__是“NAMESPACE_NAME\FUNCTION_NAME”，类成员函数的__FUNCTION__是“FUNCTION_NAME”；
4. **__CLASS__**：如果类在namespace中定义，则其为“NAMESPACE_NAME\CLASS_NAME”，否则就是“CLASS_NAME”；
5. **__METHOD__****：该常量只能作用于类成员函数，不能作用于全局函数。在有namespace的情况下，__METHOD__是“NAMESPACE_NAME\CLASS_NAME::FUNCTION_NAME”，在没有namespace的情况下，__METHOD__是“CLASS_NAME::FUNCTION_NAME”，；
6. **__TRAIT__**：这个常量类似于__CLASS__，是在PHP 5.4.0版本随着[Trait特性](http://php.net/manual/zh/language.oop5.traits.php)引入的。如果类在namespace中定义，则其为“NAMESPACE_NAME\TRAIT_NAME”，否则就是“TRAIT_NAME”。

**--END**