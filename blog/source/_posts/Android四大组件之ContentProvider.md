---
title: Android四大组件之ContentProvider
date: 2017-06-30 07:14:38
tags:
categories: 移动端
---
ContentProvider：为存储和获取数据提供统一的接口。可以在不同的应用程序之间共享数据。Android已经为常见的一些数据提供了默认的ContentProvider。

<!-- more -->

ContentProvider的相关概念：

* 跨进程数据的访问：自定义的服务端程序、通讯录等

* 设备使用ContentProvider进行存储

* 数据更新监听


如何实现数据访问：

* ContentResolver：Context.getContentResolver()，提供四个与ContentProvider相一致的方法

* Uri：统一资源标识符，用于访问ContentProvier

设备数据访问：

* 存放位置
* 特点：比如联系人信息以数据库形式存储，头像以文件形式存储
* Uri：地址，比如ContactsContract.Contacts.CONTENT_URI

设备数据更新：

* 注册内容观察者：

```
	ContentObserver observer = new ContentObserver(mHandle) {
		public void onChange(boolean selfChange,android.net.Uri uri) {
		
		}
	}
	this.getContentResolver().registerContentObserver(ContactsContract.Contacts.CONTENT_URI,true,observer);
```

