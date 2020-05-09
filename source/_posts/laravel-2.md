---
title: Laravel之旅 - 善用Migration管理資料表
date: 2020-04-25 21:11:35
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
本週來聊聊migration這個東西吧～ 
<!-- more -->
本來想想先寫Controller的，

後來想一想...

再寫一個系統時，會先將資料庫建置好，

因此先來聊聊Database這個資料夾裡可以做哪些事情～

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

***
Database這個資料夾中，

預設會有三個資料夾，分別為 factories、migrations、seeds，

這三個的功用為 設計假資料、DB Schema、匯入假資料，

我們先來說說 factories 吧！

## factories 假資料製造工廠

官方很貼心的建立一個UserFactory.php在資料夾中，

讓我們來仔細觀察一下這個檔案
{% codeblock lang:php %}
// 要生成假資料的model
use App\User;
// 引入facker與str函式
use Faker\Generator as Faker;
use Illuminate\Support\Str;
// 他呼叫了 User 這個 model，可以先看一下 app/User.php裡寫了什麼
$factory->define(User::class, function (Faker $faker) {
    return [
        // 用fake產生一個假的名字
        'name' => $faker->name,
        // 用faker產生一組email
        'email' => $faker->unique()->safeEmail,
        // 除了用faker，也可以呼叫helper、字串、數值...等
        'email_verified_at' => now(),
        'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
        'remember_token' => Str::random(10),
    ];
});
{% endcodeblock %}
這是比較簡單使用的範例，

factory 除了 define as 還有 state，

因為某些資料表會有狀態的變化，因此laravel也有提供這個方法，讓我們可以產生出對應的假資料，
{% codeblock lang:php %}
// 如果是amdin該欄位會是1
$factory->state(App\User::class, 'admin', [
    // isAdmin欄位預設為0，需到migration設定
    'isAdmin' => 1',
]);
// 到seed的地方會介紹如何使用factory state
{% endcodeblock %}

觀察完這個檔案後，該如何加入一個新的factory呢！？
```
1. 複製大法 ((cp UserFactory.php PostFactory.php
2. 指令建立 php artisan make:factory PostFactory
```
還是建議各位用2少用一點1，雖然趕時間的時候可以加快，

但容易遺漏舊的內容在裡面，有bug的話也會無限複製下去。

指令下完後，就會出現 PostFactory.php在/database/factories/裡了，

讓我們稍微做一個文章資料表的假資料吧！
{% codeblock lang:php %}
use App\Post;
use Faker\Generator as Faker;

$factory->define(Post::class, function (Faker $faker) {
    return [
        'user_id' => 1,
        'title' => 'About '.$faker->name,
        'info' => $faker->realText
    ];
});
{% endcodeblock %}

以上就是假資料製造的方法，製假資料就是這麼簡單，

更詳細的faker函式請左轉[facker的github](https://github.com/fzaninotto/Faker)
想看更完整解釋factory的話請右轉[Laravel-Writing Factories](https://laravel.com/docs/6.x/database-testing#writing-factories)

只要在這裡定義好一筆資料的內容格式，待會再seeds中就可以跟它說要製造幾筆...等功能。

***
## migrations 資料庫遷移

這裡主要都是放DB schema，再透過指令執行，就可以快速生成一個DB，

通常公司的案子幾乎都已經建立好DB，不需要在這裡一一建立，

當遇到新專案的時候，很建議可以在這裡做一份DB schema，

因為

1. 寫test時會用到
2. 不用擔心忘了備份資料庫 ((通常是不會拉，這很重要
3. 新的開發人員可以快速建置專案
4. 不需要將db.sql檔案傳來傳去的，migration也可以做到版本控制

有了這些好處，是不是該做一份schema在這裡了呢！？

但... 有人會說，

現有專案該怎麼辦，該不會要一一建立，

其實不用，

Mysql Workbanch有外掛可以用，下一章就來講一下該如何使這個外掛。

先回到我們的 migration，

一樣laravel提供了三張table的schema，

讓我們來觀察一下2014_10_12_000000_create_users_table.php，

{% codeblock lang:php %}
// 引入 laravel的migration跟其他相關的code
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

// 他是繼承Migration
// 好奇的話也可以將 Illuminate\Database\Migrations\Migration打開來觀察
class CreateUsersTable extends Migration
{
    public function up()
    {
        // table name, 生成schema的function
        Schema::create('users', function (Blueprint $table) {
            // bigIncrements就是primary key + 流水號
            $table->bigIncrements('id');
            // string 則生成 varchar
            // 如果要限制長度的話基本上都是 type('columns name', 'limit length')
            // ex. $table->string('name', 10); 長度10的文字
            $table->string('name');
            // unique() 表示該欄為唯一值
            $table->string('email')->unique();
            // nullable() 表示該欄位允許空值null
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            // default() 表示預設值
            $table->unsignedTinyInteger('isAdmin')->default(0);
            // laravel的rememberToken欄位
            $table->rememberToken();
            // 會自動建立時間戳記 created_at 和 updated_at，用途在Model章節再說明
            $table->timestamps();
        });
    }
}
{% endcodeblock %}

觀察完這個檔案後，一樣來建立一個文章資料表的schema吧！
```
// 建立migration
php artisan make:migration post
```
執行後就會得到一個檔名為 YYYY_MM_DD_timestamp_post.php

讓我們來完成它吧
{% codeblock lang:php %}
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            // 流水號
            $table->bigIncrements('id');
            // 使用者id
            // comment() 欄位的註解
            $table->unsignedInteger('user_id')->comment('使用者id');
            // 文章標題，限50字
            $table->string('title', 50)->comment('文章標題');
            // 文章內容
            $table->text('info')->comment('文章內容');
            // 時間戳記
            $table->timestamps();
            // 刪除資料的時間戳記，用該欄位是否為null判斷文章是否被刪除
            $table->softDeletes();
        });
    }
{% endcodeblock %}
就這樣，我們可以建立一張table的schema，

接下來就可以來匯入schema拉～
```
// 執行匯入資料庫
// 若遇到權限不足的問題，在前面加上sudo就可以，但建議查一下為什麼權限不足
php artisan migrate

// 清空資料庫
php artisan migrate:reset

// 回到上一個版本
php artisan migrate:rollback

// 清空再寫入
php artisan migrate:fresh
```
匯入完成後打開資料庫就可以看到資料表結構了！
![db1](../../../../image/laravel-2/db1.png "db1")

更詳細的欄位使用方式請看者裡[Laravel-Migrations](https://laravel.com/docs/6.x/migrations)

***
## Seeds 匯入假資料

當我們建立好table和假資料工廠後，就可以來匯入假資料拉～～

當然，laravel預設也是給了DatabaseSeeder.php這個檔案，

裡頭居然是註解！！！

沒關係，看一下官方文件就可以自己寫一個簡單的匯入，
{% codeblock lang:php %}
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    use App\User;
    use App\Post;

    /**
     * Seed the application's database.
     *
     * @return void
     */
    public function run()
    {
        // 這行的用意在於，官方建議我們建立每一張table的seeder，然後放到這裡來統一執行，否則指令就得一直下
        // $this->call(UsersTableSeeder::class);
        // 不過為了方便我們就直接在這裡寫使用factory
        // 生成User table的假資料三筆
        factory(User::class, 3)->create();
        // 生成User table的假資料兩筆，且isAdmin=1
        factory(User::class, 2)->states('admin')->create();
        // 生成Post table的假資料
        // 假設已經建立一個Post.php的Model
        factory(Post::class, 3)->create();
    }
}
{% endcodeblock %}
這裡小說明一下，若想用一個欄位存 0,1,2...等來分辨資料類型，就必須分開使用states，

ex. 
{% codeblock lang:php %}
factory(App\User::class, 3)->states('admin', 'user', 'member')->create();
{% endcodeblock %}
若這樣寫，最後該欄位都會是member state的值！！！
{% codeblock lang:php %}
factory(User::class, 2)->states('admin')->create();
factory(User::class, 3)->states('user')->create();
factory(User::class, 4)->states('member')->create();
{% endcodeblock %}
用這樣就不會造成state被覆蓋的問題！！！

當初看到state以為可以states('admin', 'user', 'member')，

弄了半天資料一直都是最後的state，

後來才發現不能這樣寫，或許有辦法可以做到不需要寫三次... 

把上面的方法寫完後，我們就可以開始匯入拉～
```
// 建立seeder
php artisan make:seeder ModelTableSeeder

// 執行匯入
php artisan db:seed --class=DatabaseSeeder
```
匯入完成後就可以看到DB有資料了～
posts table
![db2](../../../../image/laravel-2/db2.png "db2")
users table
![db3](../../../../image/laravel-2/db3.png "db3")

更完整的方法就在[Laravel-Seeding](https://laravel.com/docs/6.x/seeding)

***
以上是Laravel建立資料庫與假資料的方法，

其實也不一定要用到factory，

如果資料型態比較特殊的話，也可以在Seeder裡用 ``` DB::table()->insert() ``` 的方法
{% codeblock lang:php %}
// 這裡用到Carbon套件來使用時間相關的函式
DB::table('posts')->insert([
    ['user_id' => 1 ,'title' => 'About post title', 'info' => 'post content', 
        'created_at'=>Carbon::now() ,'updated_at'=>Carbon::now() ,'deleted_at' => null
    ],
    // factory 就比較難隨機產生 user_id 這欄，所以剛剛那都填user_id=1
    ['user_id' => 2 ,'title' => 'About post title2', 'info' => 'post content2', 
        'created_at'=>Carbon::now() ,'updated_at'=>Carbon::now(), 'deleted_at' => null
    ],
]);
{% endcodeblock %}
以上的方法等同於 ``` factory(Post::class, 2)->create(); ``` ，

沒有人說Seed一定要怎麼用，

怎麼用才會比較方便而是由自己決定的！！！

對於我來說，我會選擇 ``` DB::table()->insert() ``` 的方式，

因為有些資料表可以先做好預設值，

例如：開發者帳號、商品單位...等，這些屬於要先做好的資料。

***
後記

剛開始學Laravel的時候，都是到phpmyadmin開好資料表，

然後到在匯出放到專案資料夾底下做git版控，

但要分享給別人開發之後，就會有忘記將自己版本弄下來的情況，

後來了解到這塊，在日後的分工也不用擔心資料庫忘了給或給錯版本，

而且當系統測試到資料髒亂時，也可以很快的還原出一個乾淨的版本。
