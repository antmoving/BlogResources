---
title: PHP基础知识点回顾
date: 2017-08-25 19:43:14
tags:
categories: PHP
---
本文回顾了PHP的基础知识点，通过阅读电子书教程回顾下忘记的知识点。

<!-- more -->

#### 导入文件

* `include "function.php"`：发生错误时只会警告
* `include_once "function.php"`
* `require "function.php"`：发生错误时会停止脚本执行
* `require_once "function.php"`

使用`__autoload`函数实现自动加载：

		function __autoload($class_name) {
			include $class_name . '.php';
		}


#### 命名空间

* 命名空间定义：`namespace sup\sub`
* 命名空间使用：`use sup\sub`

#### 类的使用

* 实例方法、变量

		$man -> name;
		$name -> speak();
* 静态方法、变量

		Man::$Eyes;
		Man::Walk();