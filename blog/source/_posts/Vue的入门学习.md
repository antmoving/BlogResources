---
title: Vue的入门学习
date: 2017-08-29 22:31:31
tags:
categories: PHP
---
这篇博客是对Vue的入门学习，记录Vue的基本知识点。

<!-- more -->

#### 搭建本地开发环境

* 安装`live-server`：

		npm install -g live-server
* 运行命令

		$ live-server --browser=chrome --entry-file=index.html

#### 入门知识学习

Vue的核心是通过模板语法响应式地将数据渲染到DOM上。

* 文本插值 

```

	<div id="app">
		{{message}}
	</div>

	var app = new Vue({
		el: '#app',
		data: {
			message: 'Hello Vue!'
		}
	});

```


* 指令: `v-bind`

```

	//span元素的title属性与Vue实例的message属性保持一致
	<div id="app2">
		<span v-bind:title="message">
			Hover your mouse over me
		</span>
	</div>

	var app2 = new Vue({
		el: '#app2',
		data: {
			message: 'You loaded this page on ' + new Date().toLocaleString()
		}
	});

```

* 条件`v-if`和循环`v-for`

```

	<div id="app3" >
		<p v-if="seen">Invisible</p>
	</div>

	var app3 = new Vue({
		el: '#app3',
		data: {
			seen: false
		}
	});

	<div id="app4">
		<ol>
			<li v-for="todo in todos">
				{{todo.task}}
			</li>
		</ol>
	</div>

```

* 处理用户输入：使用`v-on`添加事件处理器

```

	<div id="app5">
		<p>{{message}}</p>
		<button v-on:click='reverseMessage'>Reverse Message</button>
	</div>

```

* 双向绑定：`v-model`

```

	<div id="app6">
		<p>{{message}}</p>
		<input v-model="message">
	</div>

	var app6 = new Vue({
		el: '#app6',
		data: {
			message: 'Hello'
		}
	});

```

* 组件

```

	组件本质上是带有预定义选项的Vue实例。
	注册组件
		Vue.component('todo-item', {
  			template: '<li>This is a todo</li>'
		})
		
		Vue.component('todo-item', {
			props: ['todo'],
  			template: '<li>{{ todo.text }}</li>'
		})

```

