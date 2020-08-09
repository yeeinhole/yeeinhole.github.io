---
title: Laravel之旅 - 好的Route帶你上天堂
date: 2020-08-07 18:07:48
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
好的route帶你上天堂～～

為什麼呢！？

因為好好規劃後可以不用再為了不知道用哪個單子而苦惱了，

也會在維護的時候清楚明瞭問題在哪。

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
[Laravel之旅 - 在Controller中開始寫寫code摟](https://yeeinhole.github.io/2020/08/02/laravel-4/)
***
Laravel比起原生的php，他不是以檔名作為路由的根據，

它提供了routes去建置維護整個專案的路由，

而且非常方便，有很多方法可以減少重複寫的情況，

也可以針對特定路由加上權限(can)或者是中介層(middleware)，

甚至在api的路由中，也可以寫上scope(搭配laravel passport)，

來看看routes/web.php有什麼吧！！！

{% codeblock lang:php %}
<?php

Route::get('/', function () {
    return view('welcome');
});
{% endcodeblock %}
建立好laravel的專案，都會有這個route，

可以在route中直接寫一個function且回傳值或view，

要debug一些東西或者小測試時都很方便，不用再開一隻Controller，

一般情況則是這樣寫路由的
{% codeblock lang:php %}
<?php
// 或者是 post、put、delete 這些http method
Route::get('test', 'UserController@user');

// 可以幫路由取別名，用在 return route('test');
Route::get('test', 'UserController@user')->name('test');

// 可以加上middleware
Route::get('test', 'UserController@user')->name('test')->middleware('auth');

{% endcodeblock %}
這是一般的寫法，當路徑有很多的時候可以善用group的功能，

group可以減少每段route的長度，也可以針對某些group設定上middleware和scope，

這樣就不需要每個route都要寫得很長或者要重複加上其他的設定。
{% codeblock lang:php %}
// prefix 這個group都擁有的路徑
// namespace 這個group裡的檔案路徑
// middleware 這個group要套用的中介層
Route::group(['prefix' => '/backend/', 'namespace' => 'Backend', ['middleware' => ['auth']]], function()
{
    // middleware用陣列的原因是可以加入很多個middleware
    // 裡面的寫法也是跟平常一樣
    Route::get('test', 'UserController@user');
});
{% endcodeblock %}

也可以在路由中帶上參數
{% codeblock lang:php %}

// 用{}就可以作為參數，可以帶很多個
// e.g. http://localhost/test/123
Route::get('test/{param}', 'UserController@user');

// 帶上許多參數時，但其中可能會沒有值得時候
// e.g. http://localhost/test/123
// 看上去跟前一個範例一樣，但是在程式中，需要用 $param2 才能得到值
Route::get('test/{param1?}}/{param2}', 'UserController@user');

// 這樣寫route時，function要這樣配合，就可以抓到值了
public function user($param1, $param2)
{
    $users = $this->userModel->all();
    dd($users);
}

{% endcodeblock %}

以上是我在laravel中常用到的route寫法，

官方很貼心的把api、console、channels分開來，這樣就不會導致web有很多路徑分不清楚，

``` localhost/api/XXX ```這樣寫就會是讀api.php的設定，其他兩個也是這樣，但其實我也還沒用到這兩個部分。

***
後記

想當初在寫route的時候，不知道哪根筋不對，

很有強迫症的把它tab的漂漂亮亮的，

覺得整齊看起來很舒壓 ＸＤ～