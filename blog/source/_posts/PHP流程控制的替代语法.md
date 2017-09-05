---
title: PHP流程控制的替代语法
date: 2017-09-05 23:30:50
tags:
categories:
---
这篇文章主要记录下PHP流程控制的替代语法

<!-- more -->

我们经常在wordpress一类博客程序的模板里面看到很多奇怪的PHP语法，比如：

```

<?php if(empty($GET_['a'])): ?>
   <font color="red">空的</font>
<?php endif ?>

```

上面都是PHP流程控制的替代语法，只不过不常用而已。

替代语法就是语法的另一种形式的写法。`if`、`while`、`for`、`foreach`、`switch`这些流程控制语句都有对应的替代语法。

替代语法的基本形式就是：`左花括号`替换成`冒号`，`右花括号`替换成`endif`、`endwhile`、`endfor`、`endforeach`、`endswitch`。

使用替代语法一般用在PHP和HTML混合页面的代码里面，使HTML和PHP混合页面代码更加干净整齐。
