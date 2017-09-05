---
title: Laravel之.env文件与config下文件的配置
date: 2017-09-02 09:48:41
tags:
categories:
---
config目录下存放的是应用的配置文件，而.env文件位于项目根目录下，作为全局的环境配置文件。

<!-- more -->

`.env`文件实际是对`config`目录下的通用配置做提取，在`config`目录中的文件，系统提供`env()`用于读取`.env`文件配置。


```

'default' => env('DB_CONNECTION', 'mysql'),

```

应用从`config`目录下的文件中读取配置，调用`env()`函数来读取`.env`文件中的全局通用配置，如果没有，则使用默认值。所以，应用会优先读取`.env`文件中的配置。

`config()`函数用来读取`config`目录下的项目配置项信息。

```

config('database.connections.mysql.prefix')

```



