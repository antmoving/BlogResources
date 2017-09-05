---
title: Laravel学习之视图
date: 2017-08-31 22:00:29
tags:
categories: PHP
---
这篇文章主要记录Laravel框架的视图部分学习。

<!-- more -->

#### 创建视图

视图包含应用提供的HTML，将控制器/应用逻辑和展示层进行分离。视图放在`resources/views`目录下。

```

<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>

```

该视图存放在`resources/views/greeting.blade.php`，可以使用全局的`view`辅助函数来返回：

```

Route::get('/',function () {
    return view('greeting',['name' => 'wflytoc']);
});

```

`view`辅助函数的第一个参数对应`resources/views`目录中视图文件的名称，第二个参数是传递给视图的数据数组。

视图可以嵌套在`resources/views`目录的子目录中，`.`可以用来引用嵌套的视图。如果你的视图存放在`resources/views/admin/profile.blade.php`中，可以这样引用：

```

return view('admin.profile', $data);

```

如果需要判断视图是否存在，可以使用`View`facade。如果视图存在的话，`exists`方法就会返回`true`。

```

if (View::exists('emails.customer')) {
    //
}

```

#### 将数据传递给视图

可以向视图传递数据数组：

```

return view('greetings', ['name' => 'Victoria']);

```

以这种方式传递信息时，数据应该是键值对形式的数组。在视图中，使用对应的key来获取每个值。除此之外，还可以使用`with`方法向视图中单个添加数据。

```

return view('greeting')->with('name', 'Victoria');

```

有时候，需要在所有的视图中共享同一组数据，可以使用`share`方法。通常，`share`方法应该在服务提供者的`boot`方法中调用。

```

public function boot()
    {
        View::share('key', 'value');
    }

```


#### View Composer

View Composer是回调或类方法，在视图被渲染的时候调用。如果视图每次被渲染的时候，想要绑定数据到上面，View Composer能够实现这样的功能。

在服务提供者中注册View Composer：

```

public function boot(){
     // 使用基于类的Composer
      View::composer(
            'profile', 'App\Http\ViewComposers\ProfileComposer'
       );

      // 使用基于闭包的Composer
       View::composer('dashboard', function ($view) {
            //
       });
}

```

注册了Composer后，每次`profile`视图被渲染时，`ProfileComposer@compose`方法就会执行。

```

 public function compose(View $view)
    {
        $view->with('count', $this->users->count());
    }

```

可以将View composer与多个视图关联，向`composer`方法的第一个参数传递视图数组：

```

View::composer(
    ['profile', 'dashboard'],
    'App\Http\ViewComposers\MyViewComposer'
);

```

View creator和View composer相似，但是它是在视图被实例化后马上就会执行，为了注册View creator，调用`creator`方法。

```

View::creator('profile', 'App\Http\ViewCreators\ProfileCreator')

```