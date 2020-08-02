---
title: Laravel之旅 - 開始寫code摟
date: 2020-08-02 16:09:48
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
已經認識資料庫如何串接與方法後，

讓我們來開始寫寫簡單的程式吧！！

<!-- more -->
***
本文所採用的環境

1. Max OSX 10.15
2. Docker version 19.03.5
3. Laradock
4. Laravel 6.0 以上 (5.8適用)
5. Mysql Workbanch 8.0.19

本系列不從部署環境開始說起，

部署環境可參考以下

1. [Laradock - 更快速優雅的建立Laravel](https://yeeinhole.github.io/2020/01/27/laradock/)
2. [Install Apache, MySQL, PHP on macOS Catalina 10.15](https://coolestguidesontheplanet.com/install-apache-mysql-php-on-macos-catalina-10-15/)
3. Win10請服用XAMPP，非專業版的Win裝docker會比較麻煩，不過也可以試試～～
***
本系列文章
[Laravel之旅 - 認識Laravel資料夾結構](https://yeeinhole.github.io/2020/04/25/laravel-1/)
[Laravel之旅 - 善用Migration管理資料表](https://yeeinhole.github.io/2020/04/25/laravel-2/)
[Laravel之旅 - 生成Migration的懶人包](https://yeeinhole.github.io/2020/04/25/laravel-2-2/)
[Laravel之旅 - DB的操作(1) DB Query builder](https://yeeinhole.github.io/2020/05/17/laravel-3/)
[Laravel之旅 - DB的操作(2) Eloquent ORM](https://yeeinhole.github.io/2020/05/24/laravel-3-2/)
[Laravel之旅 - DB的操作(3) Relationships](https://yeeinhole.github.io/2020/07/19/laravel-3-3/)
[Laravel之旅 - DB的操作(4) Relationships 特別篇](https://yeeinhole.github.io/2020/07/26/laravel-3-4/)
***
在Laravel中，撰寫程式的地方在app\Controller，