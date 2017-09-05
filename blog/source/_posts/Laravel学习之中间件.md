---
title: Laravel学习之中间件
date: 2017-09-01 07:42:01
tags:
categories: PHP
---
该篇文章记录了中间件的相关知识点。

<!-- more -->

#### 中间件的介绍

中间件用来过滤进入应用的HTTP请求。Laravel框架中包含几种中间件，例如验证、CSRF包含，所有这些中间件位于`app/Http/Middleware`目录中。

#### 定义中间件

定义中间件，只有提供的`age`大于200，才允许获取路由，否则的话，会将用户重定向到`home`。

```

class CheckAge
{
    public function handle($request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('/');
        }
        return $next($request);
    }
}

```

上述代码中，如果给定的`age`小于或等于200，中间件就会返回HTTP重定向；否则的话，请求会被传递到应用中。为了将请求进一步传递到应用中，只需调用`$request`的`$next`方法。

中间件在请求之前还是之后处理取决于中间件自身。

下面的中间件会在请求被应用处理之前执行：

```

class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {

        return $next($request);
    }
}

```

下面的中间件会在请求被应用处理之后执行：

```

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        return $response;
    }
}

```

#### 注册中间件

如果想要中间件运行在每个HTTP请求上(全局中间件)时，只需要在`app/Http/Kernel.php`类的`$middleware`属性中列出中间件类。

如果要将中间件分配给指定的路由，应该在`app/Http/Kernel.php`文件中分配给中间件一个key。默认地，该类的`$routeMiddleware`属性包含了Laravel框架提供的中间件；为了添加自己的中间件，只需将其添加到该列表中，并分配给它一个key。

```

protected $routeMiddleware = [
    'auth' => \Illuminate\Auth\Middleware
];

```

一旦中间件定义在HTTP kernel中，可以使用`middleware`方法将中间件分配给路由。

```

Route::get('checkAge',function () {
    return view('welcome');
})->middleware('check');

```

可以给路由指定多个中间件：

```


Route::get('checkAge',function () {
    return view('welcome');
})->middleware('first','second');

```

指定中间件的时候，可以传递完整的类名：

```

Route::get('checkAge',function () {
    return view('welcome');
})->middleware(\App\Http\Middleware\CheckAge::class);

```

如果想要将几个中间件放在同一组中，可以使用HTTP kernel的`$middlewareGroups`属性。

#### 中间件参数

中间件可以接收额外的参数，参数会被传递给`handle`方法。

```

 public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {

        }

        return $next($request);
    }

```

在定义路由时可以声明中间件参数，中间件名和参数用`:`隔开，多个参数使用,`隔开：

```

Route::put('post/{id}', function ($id) {
    //
})->middleware('role:editor');

```

有时候中间件需要在HTTP响应发送给浏览器后执行一些工作，例如，响应发送浏览器后，Laravel包含的session中间件会将session数据写到存储中。如果在中间件中定义了`terminate`方法，在响应发送给浏览器后会被自动执行。

```

class StartSession
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }

    public function terminate($request, $response)
    {
        // Store the session data...
    }
}

```

`terminate`方法接收请求和响应作为参数，一旦定义terminable中间件，应该将它添加到`app/Http/Kernel.php`文件中的路由中间件列表或全局中间件中。

调用中间件的`terminate`方法时，Laravel会从服务容器中解析全新的中间件实例。当调用`handle`和`terminate`方法时，如果想要使用相同的中间件实例，可以使用容器的`singleton`方法来在容器中注册中间件

