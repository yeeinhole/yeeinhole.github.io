---
title: Laravel之旅 - 在Controller中開始寫寫code摟
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
在Laravel中，撰寫程式的地方在```app\Http\Controllers```，

如果已經會PHP的話，會覺得這樣的結構很方便，

讓我們來看一下結構，一樣我們用指令的方式，建立一個Controller
```
php artisan make:controller UserController
```
之後在```app\Http\Controllers```就會看到剛剛的指令已經幫我們建立檔案了，

這時候會發現，剛建立的Controller居然有extend一隻Controller，
![controller1](../../../../image/laravel-4/controller1.png "controller1")
這個extend的controller在```app\Http\Controllers\```底下，

先來看看他裡面做了什麼，
![controller2](../../../../image/laravel-4/controller2.png "controller2")

什麼！！居然就短短的幾行，

不過卻都是很好用的模組，laravel非常的貼心，

把 登入驗證(AuthorizesRequests)、隊列(DispatchesJobs)、驗證(ValidatesRequests) 做成相關的模組，

可以很快速的達到這些事情，不用再自己一一撰寫，

以上的模組留到後面的章節，好奇的也可以先到官方文件看一遍，非常簡單與易用，
[登入驗證(AuthorizesRequests)](https://laravel.com/docs/6.x/authentication)
[隊列(DispatchesJobs)](https://laravel.com/docs/6.x/queues)
[驗證(ValidatesRequests)](https://laravel.com/docs/6.x/validation)

結合前面Model的部分，來試著撈資料吧！

{% codeblock lang:php %}
// UserController.php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\User;

class UserController extends Controller
{
    private $userModel;

    public function __construct(User $user){
        $this->userModel = $user;
    }

    public function user()
    {
        $users = $this->userModel->all();
        dd($users);
        // dd是斷言語法，執行到這裡會終止，並且把內容呈現出來，像是var_dump()。
    }
}

{% endcodeblock %}
再到router/web.php中寫下這個function的路徑
{% codeblock lang:php %}
Route::get('test', 'UserController@user');
{% endcodeblock %}
完成後打開瀏覽器，並寫下剛剛的路徑，就可以看到資料被撈出來了。
![controller3](../../../../image/laravel-4/controller3.png "controller3")
router可以撰寫 get、post、put、delete 這些http method，

那其實router也是有一些東西可以說明，到時候也會獨立成一個章節，

controller的部分就像是原生php那樣，可以直接把想要的程式寫在這裡，

其實有點不知道怎麼詳細說明Controller，所以先簡單的說明這裡可以寫寫php的code並透過```dd()```去查看，

會再拆分成
1. request 相關
2. return 相關

後續的一些laravel模組也都會用到這些，像是驗證或者是畫面的生成，

所以先簡單說一下，不然我怕這篇會不小心變成大雜燴。

***
後記

剛開始接觸Laravel的時候，就直接去controller中撰寫一些簡單的程式，

像是一些php書上的小範例，for、foreach、if、撈DB...等，

比較熟悉php後，開始會去注意看看laravel提供了哪些函式方便使用，

不然就會再造一次輪子，雖然可以練寫code技巧，但頗花時間的〜〜