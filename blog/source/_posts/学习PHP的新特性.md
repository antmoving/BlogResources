---
title: 学习PHP的新特性
date: 2017-08-25 20:56:10
tags:
categories: PHP
---
PHP是动态类型(变量使用前不需要显式声明)、弱类型语言(变量不是特定的数据类型，可以通过赋不同类型的值来改变)

<!-- more -->


PHP是动态类型(变量使用前不需要显式声明)、弱类型语言(变量不是特定的数据类型，可以通过赋不同类型的值来改变)

* 类型提示(`type hinting`)

		function register(string $email,int $age,bool $notify)
		通过在PHP文件的顶部声明declare(strict_types=1)，强制严格类型检查行为
* 返回类型提示

		function register(string $user, int $age) : bool 
