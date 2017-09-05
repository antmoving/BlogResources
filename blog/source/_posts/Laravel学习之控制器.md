---
title: Laravel学习之控制器
date: 2017-09-01 07:42:10
tags:
categories: PHP
---
这篇文件记录了学习Laravel控制器的相关知识。

<!-- more -->

将所有的请求处理逻辑作为闭包定义在路由文件中是很不合理的，相反，应该使用控制器类来组织。控制器类可以将相关的请求处理逻辑放在单一类中。控制器存放在`app/Http/Controller`目录下。

#### 基本的控制器

定义一个控制器，继承自Laravel包含的基控制器，该基类提供了一些便利方法，例如`middleware`方法，用来将中间件放在控制器动作上。

```

namespace App\Http\Controllers;

class UserController extends Controller {
    public function show($age) {
        return view('user.profile',['username' => 'Weichuang','age' => $age]);
    }
}

```

定义一个路由来调用这个控制器动作：

```

Route::get('user/{age}','UserController@show');

```

当请求匹配该路由URI时，`UserController`类的`show`方法就会被执行，路由参数也会被传递给该方法。


注意：在定义控制器路由时，不必写出完整的控制器命名空间。因为`RouteService`是在路由组中加载文件，已经包含了命名空间，因此只需声明命名空间`App\Http\Controllers`后面的部分。

如果定义只处理单个动作的控制器，可以在控制器中放`__invoke`方法。

```

class ShowProfile extends Controller
{

    public function __invoke($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}

```

为单个动作控制器注册路由时，不必声明方法。

```

Route::get('user/{id}', 'ShowProfile');

```

#### 控制器中间件

中间件可以分配给路由文件中的控制器路由：

```

Route::get('profile', 'UserController@show')->middleware('auth');

```

或者，在控制器的构造函数中声明中间件。在控制器的构造函数中，使用`middleware`方法可以将中间件分配给控制器的动作。甚至可以将中间件限制到控制器的特定方法上。

```

 public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('log')->only('index');

        $this->middleware('subscribed')->except('store');
    }

```

控制器也允许你使用闭包来注册中间件。

```

$this->middleware(function ($request, $next) {
    // ...

    return $next($request);
});

```

#### 资源控制器




