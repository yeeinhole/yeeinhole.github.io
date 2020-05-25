---
title: Laravel之旅 - DB的操作(2) Eloquent ORM
date: 2020-05-24 23:11:07
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
在前一篇我們提到laravel有兩種操作模式，

本篇來說說 Eloquent ORM 吧！

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
[Laravel之旅 - DB的操作(1) DB Query builder](https://yeeinhole.github.io/2020/04/25/laravel-3/)
***

在前一篇有提到，Eloquent ORM需要維護Model，

所以我們就從Model開始說起吧！！！

在laravel初始時，有給了一個User.php，

我們也在Migration的時候就已經使用過該檔案了，

讓我們來看一下他裡頭有什麼吧，

### Model - User.php
{% codeblock lang:php %}

<?php

namespace App;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password', 'isAdmin'
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];

    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
}

{% endcodeblock %}

``` Notifiable ``` 是Laravel通知相關的模組，除了寄email以外還可以寄SMS、Slack，

本章節不會解釋該模組可以做什麼，有興趣可先看看這裡[Laravel - Notifications](https://laravel.com/docs/6.x/notifications#introduction)

``` $fillable ``` 這個陣列裡主要放table中有哪些column，可以定義也可以不用，

若漏定義的話，在insert的過程會漏掉columns，我通常都會定義它，

``` $hidden ``` 當model的查詢結過使用``` toArray() ```輸出時，

將會隱藏該欄位的值，這很方便可以將密碼欄位隱藏起來，

``` $casts ``` 主要用在值得轉換，最簡單的例子就是 ``` true / false ``` 的值用 ``` 0 / 1 ``` 的方式儲存在資料庫中，

以上是User.php的內容，但還有一些比較常用的，

{% codeblock lang:php %}

// 資料表的名稱
protected $table = 'users';

// 預設的主鍵名稱為 id，若不是則需要設設定 
protected $primaryKey = 'user_id';

// 若主鍵不是使用流水編號 ex. 1,2,3.....N 
public $incrementing = false;

// 主鍵的類型
protected $keyType = 'string';

// 使不使用時間戳記，預設為true
// 使用時間戳必須要有 created_at 和 updated_at 這兩個欄位
public $timestamps = false;

// 設定時間戳的欄位名稱
const CREATED_AT = 'creation_date';
const UPDATED_AT = 'last_update';

// 也可以獨立設定資料表的連線方式，設定後該資料表存取時就不會去使用.env中的方式
protected $connection = 'connection-name';

{% endcodeblock %}

以上是Model中比較常用的設定，

讓我們來看看如何使用Model操作吧！！！

使用Model操作DB後，會回傳Collections型態的array，

Collections也提供了一些方法可以再查詢資料...等，

可以先看看這裡[Laravel - Eloquent: Collections](https://laravel.com/docs/6.x/eloquent-collections#available-methods)

以下是 Eloquent ORM的基本使用方法，

### Eloquent ORM的CRUD
{% codeblock lang:php %}
// 拿User這個model舉例

// 查詢PK = 1的資料
User::find(1);

// 查詢多筆資料 By PK欄位
User::find([1, 2, 3]);

// Where條件式，第一個參數為欄位名稱，第二個參數為值
User::where('name', 'Yee');

// Where也可以這樣用
User::where('age', '>', '18');

// Where還能這樣用 ((我最愛這方法ＸＤ
$query = [['name', '=', 'Yee'], ['age', '>', '18']]
User::where($query);

// 當然也有where NULL
User::whereNull('name')->get();
User::whereNotNull('name')->get();

// Insert 的方法一
$user = new User;
$user->name = 'Yee';
...
$user->save();

// Insert 的方法二
$attributes = [['name'=>'Yee', 'email'=>'yee@gmail.com']];
$user = User::create($attributes);

// 兩個新增的方法都會讓 $user 擁有剛剛新增得值

// Update 的方法一
$user = User::find(1);
$user->name = 'Yee2';
$user->save();

// Update 的方法二
// where條件請參考上面的方法
$user = User::where($query)->update($attributes);

// Delete 的方法，
User::find(1)->delete();

{% endcodeblock %}

建議使用 ``` deleted_at ``` 欄位，可以存時間戳或者 ``` true / false ``` 來表示該筆資料是否被刪除，

``` delete_at ``` 在 migration 中為 ``` $table->softDeletes(); ```，

我都稱他為軟刪除，可以讓使用者操作刪除，但不真實的把資料刪除，

可以避免user手殘...等意外，也能有完整的資料流，

以上的條件式的結尾可以用
{% codeblock lang:php %}
// 取第一筆
User::find(1)->first();

// 取全部
User::where('age', '>', '18')->get();
{% endcodeblock %}

更進階的用法有這些
{% codeblock lang:php %}
// 有資料回傳資料否則拋出Exception
User::find(1)->firstOrFail()

// 有資料回傳資料，否則使用另一個條件式查詢
User::where('name', 'Yee')->firstOr(['name', 'Yee2'])

// 有資料就回傳，否則新增一筆資料，方法一
$user = User::firstOrNew(
    ['email' =>  'yee@gmail.com'],
    ['name' => 'yee']
);

$user->save();

// 有資料就回傳，否則新增一筆資料，方法二
// 這方法不需要 $user->save();
$user = User::firstOrCreate(
    ['email' =>  'yee@gmail.com'],
    ['name' => 'Yee']
);

// 有資料就更新，否則新增一筆資料
$user = User::updateOrCreate(
    ['email' =>  'yee@gmail.com'],
    ['name' => 'Yee']
);

{% endcodeblock %}

以上是我比較常用跟常見的操作方法，

更多方法可以看這邊[Laravel - Eloquent](https://laravel.com/docs/6.x/eloquent)

***
後記

某些成面來說 Eloquent ORM 跟 Query builder的用法蠻像的，

所以Query builder有的方法，在Eloquent ORM也大部分都有，只是要再查詢一下用法，

Eloquent ORM 除了以上的操作外，也可以設定資料表關聯，

可以免除寫很多join的條件式，

下篇再來跟大家講解 relationships 的部分。
