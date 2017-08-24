---
title: iOS相关问题
date: 2017-07-03 13:22:55
tags:
categories: 问题记录
---
本篇博客记录在学习、开发iOS过程中遇到的一些问题，通过记录这些问题，能够减少以后可能会遇到相同问题解决的时间。

<!-- more -->

#### 1.使用Autolayout在同一行同时约束两个控件(没有约束宽度)，当第二个控件内容太多时，第一个控件内容无法完全显示。

		解答：通过设置两个控件的Content Hugging Priority(控件被拉伸的优先级)和Content Compression Resistance Priority(控件抗压缩的压缩的优先级)
