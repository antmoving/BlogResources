---
title: Laravel学习之路由
date: 2017-08-30 21:23:17
tags:
categories: PHP
---
本篇博客记录了Laravel的路由相关知识点，路由是非常重要的部分。

<!-- more -->

#### 基本的路由

最基本的路由是接收一个URI和闭包：

```

Route::get('basic',function () {
    return 'Hello World';
});

```

所有的路由都是定义在路由文件中，位于`routes`目录下，路由文件会被框架自动加载。`routes/web.php`文件为web接口定义了路由，这些路由被分配给`web`中间件组(提供了session状态和CSRF保护)。`routes/api.php`文件中的路由是无状态的，会被分配给`api`中间件组。

路由器可以让你注册对应的路由来响应任何的HTTP动词。

```

Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);

```

可以使用`match`方法注册路由来响应多个HTTP动词，或者使用`any`方法注册路由来响应任何HTTP动词。

```

Route::match(['get','post'],'/basic/match',function() {
    return "/basic/match";
});
Route::any('/basic/any',function() {
    return "/basic/any";
});

```

指向`web`路由文件中路由的HTML表单应该包含一个CSRF token域，否则的话，请求会被拒绝。

#### 路由参数

如果需要从路由中获取URI的片段，可以定义路由参数：

```

Route::get('user/{id}',function ($id) {
    return 'User '.$id;
});

```

可以定义许多路由参数，路由参数总是包含在大括号里，路由参数会基于顺序被注入到路由回掉或控制器中。

```

Route::get('/user/{group}/{team}',function ($group,$team) {
    return 'group '.$group.' team '.$team;
});

```

如果需要声明一个路由参数是可选的，可以在参数名后面放一个问号，并且该参数对应的变量值要有一个默认值。

```

Route::get('/option/{name?}/age',function ($name = 'God') {
    return $name;
});

```


可以使用路由实例的`where`方法来约束路由参数的格式，该方法接收参数的名称和约束参数的正则表达式。

```

Route::get('user/{id}',function ($id) {
    return 'user '.$id;
})->where('id','[0-9]+');

Route::get('user/{id}/{name}',function ($id,$name){
    return 'id '.$id.' name '.$name;
})->where(['id'=>'[0-9]+','name'=>'[a-z]+']);

```

如果想要一个路由参数总是被给定的正则表达式约束(全局约束)，那么可以使用`pattern`方法。应该在`RouteServiceProvider`的`boot`方法里定义这些`pattern`方法。

```

public function boot(){
    Route::pattern('id','[0-9]+');
    parent::boot();
}

Route::get('user/{id}',function ($id) {
    return 'user id '.$id;
});

```

#### 命名的路由(路由的别名)

命名的路由可以方便产生URL或重定向到特定的路由，可以调用`name`方法来为路由声明一个名字。

```

Route::get('user/profile',function () {
    return 'user profile';
})->name('profile');

```

一旦为给定的路由分配了名字，可以通过全局的`route`函数来产生URL或重定向。

```

$url = route('profile');
return redirect()->route('profile');

```

如果命名的路由定义了参数，则将其作为第二个参数传递给`route`函数。给定的参数会被自动插入到URL中。

```

Route::get('user/{id}/profile',function ($id) {
    return 'user profile '.$id;
})->name('profile');
$url = route('profile',['id'=>1]);

```

如果想要判断当前的请求是否到达指定的命名路由，可以调用Route实例的`named`方法。

```

if ($request->route()->named('profile')) {
   //
}

```

#### 路由组

路由组可以在许多路由中共享路由属性，例如中间件或命名空间，而不必单个来定义这些属性。

为了分配中间件(`middleware`)给路由组中的所有路由，可以在定义路由组前使用`middleware`方法。

```

Route::middleware(['first','second'])->group(function () {
            Route::get('/',function () {
                return '';
            });
            Route::get('user/profile',function () {
                return '';
            });
});

```

路由组的另一个常见用法是为一组控制器分配相同的PHP命名空间，使用`namespace`方法。

```

Route::namespace('Admin')->group(function () {
    // Controllers Within The "App\Http\Controllers\Admin" Namespace
});

```

`prefix`方法可以用来为路由组中的每个路由添加前缀。

```

Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});

```



#### 路由模型绑定

#### 表单方法"伪造"

HTML表单不支持`PUT`、`PATCH`和`DELETE`动作，为了让表单能够调用这些路由，需要向表单添加隐藏的`_method`域，对应的值就是HTTP请求方法。

```

<form action="/foo/bar" method="POST">
    <input type="hidden" name="_method" value="PUT">
    <input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>

```

可以使用`method_field`辅助方法来产生`_method`元素：

```

{{ method_field('PUT') }}

```

#### 获取当前路由

可以使用`Route`facade来获取正在处理的请求的路由信息。

```

$route = Route::current();

$name = Route::currentRouteName();

$action = Route::currentRouteAction();

```





