---
title: WordPress开发教程(三)之开发主题
date: 2017-09-04 23:15:16
tags:
categories:
---
#这里写上文章显示的摘要

<!-- more -->

WordPress主题是一系列文件，在底层统一的设计下，共同协作来构建一个图形化界面。简单地说，主题为你的网站提供了皮肤，允许你修改内容展示的方式。

在这篇教程中，我们将学习主题开发。首先了解组成主题的基本文件，接着学习模板，模板标签和循环。

#### 开始学习 

主题通常存放在`wp-conent/themes`目录中。如果进入到那个目录，你会发现里面已经有了几个主题，那是默认的WordPress主题。

下面让我们来创建一个新的主题：

* 在`themes`目录下创建文件夹，命名为`my-awesome-theme`


* 在新的文件夹里，创建两个文件：`style.css`和`index.php`

打开样式表文件，粘贴下面的代码 [官方实例代码](https://developer.wordpress.org/themes/basics/main-stylesheet-style-css/)：

```

/*
Theme Name: Awesome Theme
Theme URI: http://somethingaboutchina.com
Author: wcshinestar
Author URI: https://antmoving.github.io
Description: Awesome Theme brings your site to life with immersive featured images and subtle animations. With a focus on business sites, it features multiple sections on the front page as well as widgets, navigation and social menus, a logo, and more. Personalize its asymmetrical grid with a custom color scheme and showcase your multimedia content with post formats. Our default theme for 2017 works great in many languages, for any abilities, and on any device.
Version: 1.0
License: GNU General Public License v2 or later
License URI: http://www.gnu.org/licenses/gpl-2.0.html
Text Domain: my-awesome-theme
Tags: one-column, two-columns, right-sidebar, flexible-header, accessibility-ready, custom-colors, custom-header, custom-menu, custom-logo, editor-style, featured-images, footer-widgets, post-formats, rtl-language-support, sticky-post, theme-options, threaded-comments, translation-ready
This theme, like WordPress, is licensed under the GPL.
Use it to make something cool, have fun, and share what you've learned with others.
*/

```

上面的信息会自动填充WordPress后台的`Appearance > Theme`页面中的"主题详情"部分。上面的评论中必须要包含主题的名字(其它是可选的，但是推荐填完所有信息)。注意：`text Domain`必须要准确匹配文件夹的名字(本例子中是`my-awesome-theme`)。

现在如果你导航到`Appearance -> Themes`部分，应该可以看到你的主题出现在列表中，甚至可以启动它。

#### 模板的工作原理

WordPress主题使用模板文件进行工作的，它实际上是很巧妙的方法，与普通的HTML网站比较，它减少了网站运行需要的文件数量。如果你有一个HTML网站，对于每篇发表的文章都需要一个文件；而每个文件包含网站的头部(`header`)，侧边栏(`sidebar`)和尾部(`footer`)，对于每个文件来说，这都是相同的；唯一的不同之处是文章的内容。

因为PHP是在服务端处理的，我们可以更好地节约文件占有的空间。我们可以只拥有一个文件(模板文件)，检测到所在的页面，要求PHP根据实际的文章标题和内容来取代占位符。

下面使用伪代码来展示如何工作的：

```

<!DOCTYPE html>
<html>
<head>
  <title>A single post template</title>
</head>
<body>

  <div id="site-header">
    <h1>Welcome to my site</h1>
    <nav>
      <ul>
        <li><a href=''>Home</a></li>  
        <li><a href=''>About</a></li>  
        <li><a href=''>Contact</a></li>  
      </ul>  
    </nav>
  </div>
  
  <div id="article">
    <h2 class="article-title"><?php the_title() ?></h2>
    <div class="article-content"><?php the_content() ?></div>
    <div class="article-meta">Published on <?php the_time( "Y-m-d" ) ?> by <?php the_author() ?></div>
  </div>
  
  <div id="site-footer">
    <nav>
      <ul>
        <li><a href=''>Home</a></li>  
        <li><a href=''>About</a></li>  
        <li><a href=''>Contact</a></li>  
      </ul>  
    </nav>
    <div id="copyright">&copy; Daniel Pataki</div>
  </div>
  
</body>
</html>

```





