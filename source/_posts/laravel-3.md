---
title: Laravel之旅 - DB的操作(1) DB Query builder
date: 2020-05-17 01:05:42
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
資料庫建完了之後～ 

我們來聊聊Laravel兩種操作DB的方式～～～

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
***
在Laravel中有兩種操作DB的方法，
1. DB Query builder
2. Eloquent ORM

先來淺談一下兩者
|     |  DB Query builder  | Eloquent ORM  |
|  ----  | ----  | ----  |
|  使用方式  | DB::table()...語法  | 需建立與維護Model |
|  回傳型態  | array  | Collection |
|  方便程度  | 普通 | 很方便 |
|  彈性  | 很彈性 | 普通 |
|  維護  | 若沒有寫class，就需要到處維護 | 只需維護Model |
|  易用程度  | 普通 | 較易用 |
以上是我兩者使用過後的小心得，不一定是都是對的，

因為遇到的情況有些差異，所以有一些不一樣的感受，

但兩者都會自己轉為sql語法進行操作，

看完這個簡單的差異之後，讓我們來看看各自的用法吧！

## DB Query builder

DB Query builder的語法看起來很直覺，

只要會寫sql就可以很快速地上手它，

來看看CRUD的基礎用法吧

### Select的用法
{% codeblock lang:php %}

// 撈取users table裡所有的資料
DB::table('users')->get();

// 撈取第一筆資料
DB::table('users')->first();

// 加上where條件，預設使用 "=" 做query
DB::table('users')->where('id', 1')->first();

// 進階where條件
DB::table('users')->where('name', 'like', '%Y')->first();

// 多條件的where
$query = [['name', 'like', '%Y'], ['status', '=', '1']];
DB::table('users')->where($query)->first();

// NULL的where
DB::table('users')->whereNull('name')->get();
DB::table('users')->whereNotNull('name')->get();
// 因為null在sql中的條件是為 IS or IS NOT，
// 但laravel的query條件不能放IS，所以有提供這兩個方法操作NULL的值

{% endcodeblock %}

非常的直覺，我個人很喜歡使用多條件的where方法，

既使今天只有一個where條件也可以使用，
{% codeblock lang:php %}
$query = [['name', 'like', '%Y'],];
DB::table('users')->where($query)->first();
{% endcodeblock %}
這樣的用法，可以很靈活的用where去撈取資料，

當我要加條件時，只要像這樣
{% codeblock lang:php %}
array_push($query, ['status', '=', '1']);
// 此時 $query 長這樣 [['name', 'like', '%Y'], ['status', '=', '1']];
DB::table('users')->where($query)->first();
{% endcodeblock %}

### Insert的用法
{% codeblock lang:php %}
// 新增一筆資料
DB::table('users')->insert(
    ['name' => 'Yee', 'email' => 'Yee@mail.com', 'password' => 'YEEEEEE']
);

// 新增多筆資料
DB::table('users')->insert([
    ['name' => 'Yee', 'email' => 'Yee@mail.com', 'password' => 'YEEEEEE'],
    ['name' => 'Yee2', 'email' => 'Yee2@mail.com', 'password' => 'YEEEEEE'],
    ...,
]);

// 也可以這樣
$data = [
    ['name' => 'Yee', 'email' => 'Yee@mail.com', 'password' => 'YEEEEEE'],
    ['name' => 'Yee2', 'email' => 'Yee2@mail.com', 'password' => 'YEEEEEE'],
    ...,
]
DB::table('users')->insert($data)
{% endcodeblock %}
insert也非常的簡單，沒有很複雜

### Update的用法
{% codeblock lang:php %}
// 基本的update
DB::table('users')->where('id', 1)->update(['status' => 1]);

// 修改或者新增
DB::table('users')
    ->updateOrInsert(
        // 有沒有 john@example.com 跟 John
        ['email' => 'Yee@mail.com', 'name' => 'Yee'],
        // 有就將狀態改為2
        ['status' => '2']
        // 若沒有的話，就會新增一筆資料
    )

// 也可以修改json欄位裡的資料
// MySQL 5.7+ 以上才支援！！！
DB::table('users')
    ->where('id', 1)
    ->update(['options->enabled' => true]);

// 當需要對欄位加減1,2,3...時
// 增
DB::table('users')->increment('age');
DB::table('users')->increment('age', 2);
// 減
DB::table('users')->decrement('age');
DB::table('users')->decrement('age', 2);

// 以上用法跟以下相同
DB::table('users')
    ->update(['age' => 1]);
{% endcodeblock %}

update的部分也提供蠻簡單的方法去使用，

### Delete的用法
{% codeblock lang:php %}
// 刪除某一些資料
DB::table('users')->where('status', '=', 2)->delete();

// 清空資料庫 (會reset id index)
DB::table('users')->truncate();

{% endcodeblock %}

以上是DB Query builder 的基本用法 ～～

### 進階篇

### DB lock
有時候操作DB時，不允許其他query線程一起撈取或操作同一筆資料，

這時候我們會使用table lock，

laravel提供了兩種lock方法
{% codeblock lang:php %}
// 操作時，允許其他線程撈取資料，但不能修改
DB::table('users')->where('status', '=', 2)->sharedLock()->get();

// 操作時，不允許其他線程撈取與修改
DB::table('users')->where('status', '=', 2)->lockForUpdate()->get();

{% endcodeblock %}
通常lock我比較常用在update的情況，

例如：訂購商品時，庫存進行減1，

這個情況就得用lockForUpdate，因為更新庫存時不能給其他線程撈，

避免庫存剩下1時，大家都撈到庫存=1，後續操作可能會發生問題...等，

用lockForUpdate後，晚到的線程會等前面更新結束且unlock後才能撈得到資料

### DB Transaction
這也是一個很重要的操作，

當一個function要操作兩張資料表以上時，有可能會遇到某一張表操作失敗，

這時候若沒使用transaction，第一張表操作的結果會保留，

這樣有可能會造成資料重複...等問題，

因此可以加上transaction機制，確保每一張表的操作都成功才保存
{% codeblock lang:php %}
// 基本的transaction
DB::transaction(function () {
    DB::table('users')->where($query)->first();
    DB::table('users')->where('id', 1)->update(['status' => 1]);
    DB::table('posts')->where('id', 1)->update(['status' => 1]);
});

// 失敗後重試3次，都失敗時則拋出Exception
$n = 3;
DB::transaction(function () {
    DB::table('users')->where($query)->first();
    DB::table('users')->where('id', 1)->update(['status' => 1]);
    DB::table('posts')->where('id', 1)->update(['status' => 1]);
}, $n);
{% endcodeblock %}
用以上的方法可以不用再加上 ```DB::commit()```，執行成功時完會自動 commit，

我們也可以手動控制transaction的起始，
{% codeblock lang:php %}
// 開始transactio
DB::beginTransaction();

// 發生錯誤時取消所有操作
DB::rollBack();

// 結束 
DB::commit();
{% endcodeblock %}


更多方法可以到這裡看看
1. [Laravel-Database: Getting Started](https://laravel.com/docs/6.x/database)
2. [Laravel-Database: Query Builder](https://laravel.com/docs/6.x/queries)

下一篇再來說聊Eloquent ORM

***
後記

在介紹DB Query builder 的用法時，

去仔細閱讀了一下官方的文件，忽然發現 increment 和 decrement，

老實說... 先前都沒注意到這個用法，

默默地回去改訂單的query寫法 XDD

官方文件真的藏了好多寶，讓大家可以優雅得開發。