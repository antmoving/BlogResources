---
title: Laravel学习之数据库(1)
date: 2017-09-02 00:55:00
tags:
categories:
---
这篇文章记录Laravel框架的基础数据库知识。

<!-- more -->

#### 介绍

Laravel使得数据库操作异常简单，使用方式：原始SQL，`fluent query builder`，`Eloquent ORM`。

应用的数据库配置放在`config/database.php`，在该文件中，可以定义所有的数据库连接，并且声明默认使用哪个连接。

使用命令创建新的SQLite数据库`touch database/database.sqlite`，可以很容易地配置环境变量来指向这个新创建的数据库。

使用多个连接时，可以通过`DB`facade的`connection`方法来获取每个连接。传递给`connection`方法的`name`对应`config/database.php`配置文件中列出的连接之一。

```

$users = DB::connection('foo')->select(...);

```

可以使用connection实例对象的`getPdo`方法来获取原始的、底层的`PDO`实例。

#### 运行原始的SQL查询

一旦配置好数据库连接后，可以使用`DB`facade来运行SQL语句。`DB`facade为每种语句提供了方法：`select`、`update`、`insert`、`delete`、`statement`。

使用`DB`facade的`select`方法来运行基本的查询：

```

public function users() {
        $users = DB::select('select * from user where country = ?',['China']);
        return view('user.users',['users'=>$users]);
}

```

传递给`select`方法的第一个参数是原始的SQL查询，第二个参数是绑定到查询上的参数绑定。`select`方法总是返回`array`结果。数组中的每个值都是PHP的`StdClass`对象,允许你获取对应的值。

```

foreach ($users as $user) {
            echo 'username '.($user->name).' age '.($user->age).' country '.($user->country).'<br>';
}

```

除了使用`?`来表示参数绑定外，也可以使用命名绑定来执行查询：

```

$results = DB::select('select * from users where id = :id', ['id' => 1]);

```

使用`DB`facade的`insert`方法可以执行`insert`语句，该方法接收原始的SQL查询语句作为第一个参数，第二个参数则是参数绑定。

```

public function insertUser() {
        DB::insert('insert into user (name,age,country) values (?,?,?)',['weishanfeng',50,'China']);
        return redirect('users');
}

```

`update`方法用来更新数据库中已经存在的记录，语句影响的行数会被返回：

```

$affected = DB::update('update users set votes = 100 where name = ?', ['John']);

```

`delete`方法用来删除数据库中的记录，影响的行数会被返回：

```

$deleted = DB::delete('delete from users');

```

有些数据库操作语句不会返回任何值，对于这些类型的操作，可以使用`DB`facade的`statement`方法：

```

DB::statement('drop table users');

```

如果想要接收到应用执行的每条SQL查询语句，可以使用`listen`方法，该方法用来打印查询语句或调试，可以在服务提供者中进行注册。

```

public function boot()
    {
        DB::listen(function ($query) {
            // $query->sql
            // $query->bindings
            // $query->time
        });
    }

```






#### 数据库事务

可以使用`DB`facade的`transaction`方法来在数据库事务中执行一系列操作。如果事务闭包中发生异常，事务会自动回滚；如果闭包执行成功，事务会被自动提交。

```

DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
});

```

`transaction`方法接收可选的第二个参数，用来定义当死锁发生时，事务重新尝试的次数。一旦尝试次数超出，就会抛出异常：

```

DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
}, 5);

```

如果想要手动开始一个事务，能够完全地掌控回滚和提交，可以使用`beginTransaction`。

```

DB::beginTransaction();

DB::rollBack();

DB::commit();

```
