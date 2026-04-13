---
title: PHP数据类型
tags:
  - PHP编程
categories:
  - PHP编程
copyright: true
abbrlink: e3aca56f
date: 2020-03-12 02:19:13
---
**0x01. php数据类型**

字符串(String)、整型(Integer)、浮点型(Float)、布尔型(Boolean)、数组(Array)、对象(Object)、空值(Null)

**0x02 字符串**  
一个字符串是一串字符串的序列，比如"hello word"  
可以将任何文本放在单引号或者双引号中。

    
    
    <?php
    $sts ="你好啊，我是PHP的字符串";
    echo $sts;
    
    ?>
    

**0x03 整数**  
整数是没有小数点的数字  
整数最少必须有 0-9的一个数字  
整数不能包含逗号或者空格，可以是正整数也可以是负整数  
整数可以用：十进制、十六进制（前缀为0x）、八进制（前缀为0）表示  
var_dump()函数返回变量的数据类型和值。

    
    
    <?php
    $x = 12333;
    var_dump($x);
    echo "<br>"; 
    $x = -3433; // 负数 
    var_dump($x);
    echo "<br>"; 
    $x = 0x8C; // 十六进制数
    var_dump($x);
    echo "<br>";
    $x = 047; // 八进制数
    var_dump($x);
    
    ?>
    

**0x04 php浮点型**

浮点数是带小数部分的数字，或是指数形式。

    
    
    <?php
    
    $x = 10.365;
    var_dump($x);
    echo "<br>"; 
    $x = 2.4e3;
    var_dump($x);
    echo "<br>"; 
    $x = 8E-5;
    var_dump($x);
    
    ?>
    

**0x05 php布尔型**  
布尔型可以是TRUE或FALSE。布尔型通常用于条件判断

    
    
    <?php 
    
    $x = true;
    $y = false;
    
    ?>
    

**0x06 数组**  
数组可以在一个变量中存储多个值。

    
    
    <?php 
    
    $cars=array("Volvo","BMW","Toyota");
    var_dump($cars);
    
    ?>
    

**0x07 php对象**  
对象数据类型也可以用于存储数据。  
在 PHP 中，对象必须声明。  
首先，你必须使用class关键字声明类对象。类是可以包含属性和方法的结构。  
然后我们在类中定义数据类型，然后在实例化的类中使用数据类型：

    
    
    <?php
    class Car
    {
      var $color;
      function __construct($color="green") {
        $this->color = $color;
      }
      function what_color() {
        return $this->color;
      }
    }
    ?>
    

**0x08 php NULL值**

NULL 值表示变量没有值。NULL 是数据类型为 NULL 的值。  
NULL 值指明一个变量是否为空值。 同样可用于数据空值和NULL值的区别。  
可以通过设置变量值为 NULL 来清空变量数据：

    
    
    <?php
    
    $x="Hello world!";
    $x=null;
    var_dump($x);
    
    ?>
    

