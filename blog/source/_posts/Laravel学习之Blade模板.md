---
title: Laravel学习之Blade模板
date: 2017-09-02 13:16:59
tags:
categories: PHP
---
#这里写上文章显示的摘要

<!-- more -->

Blade是由Laravel提供的模板引擎，它可以让你在视图中编写PHP代码。事实上，所有的Blade视图都会被编译成PHP代码，被缓存起来，除非视图被修改。Blade视图文件使用`.blade.php`文件后缀名，一般会放在`resources/views`目录下。

#### 模板继承

Blade最大的好处是模板继承(`template inheritance`)和区块(`section`)。

首先，定义`master`页面布局。

```

<html>
<head>
    <title>App Name - @yield('title')</title>
</head>
<body>
    @section('sidebar')
        This is the master sidebar
    @show

    <div class="container">
        @yield('content)
    </div>

</body>
</html>

```

注意下`@section`和`@yield`指令。`@section`指令用于定义内容区块；而`@yield`指令用于显示内容区块。

接下来，定义继承布局的子页面。定义子视图时，使用Blade的`@extends`指令来声明子视图应该继承哪个布局。继承Blade布局的视图使用`@section`指令来将内容注入到布局的区块中。

```

@extends('layouts.app)

@section('title','Page Tile')

@section('sidebar')

    @parent

    <p>This is appended to the master sidebar</p>

@endsection

@section('content')

    <p>This is my body content</p>

@endsection

```

上述代码中，`sidebar`区块利用`@parent`指令来将内容追加到布局的sidebar中。视图渲染时，`@parent`指令会被布局的内容所取代。

注意：`@endsection`结尾只是定义区块内容，而`@show`结尾会定义并展示区块内容。

使用全局的`view`辅助函数可以让Blade视图从路由器那返回：

#### 组件和槽(Component & Slots)

组件和槽提供的功能与区块和布局相似。

#### 展示数据

通过将变量放在双大括号里可以展示传递给Blade视图的数据。

```

Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});

Hello, {{ $name }}.


```

当然，你不仅仅只限于显示传递给视图的变量内容，你也可以echo出任何PHP函数的结果。

```

The current UNIX timestamp is {{ time() }}

```

默认，Blade的双大括号语法会被自动发送给PHP的`htmlspecialchars`函数来处理。如果不想让你的数据被处理，可以使用下面的语法：

```

Hello, {!! $name !!}.

```

#### Blade和JS框架

因为许多JS框架也会使用双大括号语法来表明给定的表达式会被展示到浏览器上，你可以使用`@`符号来告诉Blade渲染引擎不要处理双括号。

```


<h1>Laravel</h1>

Hello, @{{ name }}.

```

如果模板中有很大部分需要展示JS变量，那么可以将HTML包装在`@verbatim`指令中，那么你就不必在每个Blade双括号前面加上`@`符号了。

```

@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim

```

#### 控制结构

除了模板继承和展示数据外，Blade也提供了常见的PHP控制结构，例如条件和循环语句。



