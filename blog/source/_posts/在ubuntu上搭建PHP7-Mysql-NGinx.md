---
title: 在ubuntu上搭建PHP7+Mysql+NGinx
date: 2017-08-24 21:41:05
tags:
categories: PHP
---
本文记录下在Ubuntu环境下搭建PHP的开发环境，通过搭建PHP7+MySQL+Nginx来打造学习PHP的环境。

<!-- more -->

#### 安装Nginx

* 安装Nginx

        sudo apt-get undate
        
        sudo apt-get install nginx
* 配置Nginx    

        打开`/etc/nginx/sites-avaliable/default`文件，修改`root`、`index`、`location`，增加`location ~ php$`部分，重启Nginx：`sudo service nginx restart`。配置文件如下：

        server {
            listen 80 default_server;
            listen [::]:80 default_server ipv6only=on;
            #root为项目代码所在目录
            #index在中间添加index.php
            root /websites;
            index index.php index.html index.htm;
            # Make site accessible from http://localhost/
            #填写外网IP或者自己已经解析的域名
            server_name http://www.somethingaboutchina.com;
            #更改未被注释一行
        
            location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ /index.php?$query_string;
                # Uncomment to enable naxsi on this location
                # include /etc/nginx/naxsi.rules
            }
            #添加下面代码
            location ~ \.php$ {
                try_files $uri /index.php =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
            }
        }

#### 安装PHP7

* 添加PPA:PPA，表示 Personal Package Archives，也就是个人软件包集

        sudo apt-get install python-software-properties software-properties-common
        sudo add-apt-repository ppa:ondrej/php
        sudo apt-get update
* 安装PHP7以及所需的一些扩展

        sudo apt-get install php7.0-fpm php7.0-cli php7.0-mcrypt php7.0-mysql php7.0-mbstring php7.0-xml
* 配置PHP7

        1. 打开`php.ini`配置文件，找到`cgi.fix_pathinfo`选项，去掉注释，然后将值设置为0 (vim使用“/”进入查找模式)
        
            `sudo vim /etc/php/7.0/fpm/php.ini`

        2. 启用php7.0-mcrypt

            `sudo phpenmod mcrypt`

        3. 重启php7.0-fpm

            `sudo service php7.0-fpm restart`

#### 安装Mysql

* 安装Mysql

        sudo apt-get install mysql-server-5.7 mysql-client-5.7

#### 安装Composer

* 安装Composer：

        到[Composer官网](https://getcomposer.org/)下载`composer.phar`

        sudo mv composer.phar /usr/local/bin/composer
* 替换Composer源

        `composer config -g repo.packagist composer https://packagist.phpcomposer.com`
