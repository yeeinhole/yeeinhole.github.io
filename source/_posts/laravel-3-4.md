---
title: Laravel之旅 - DB的操作(4) Relationships 特別篇
date: 2020-07-26 18:51:09
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
已經說明Relationships的用法，

讓我來說說它神奇的地方，

當有一張表是紀錄兩張表之間的關聯，

往往更新就需要撈出來自己比對差異，

Laravel提供了幾個函式，

可以快速的更新。

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
[Laravel之旅 - DB的操作(2) Eloquent ORM](https://yeeinhole.github.io/2020/04/25/laravel-3-2/)
[Laravel之旅 - DB的操作(3) Relationships](https://yeeinhole.github.io/2020/04/25/laravel-3-3/)
***

首先我們需要有這樣情境

|  User   |  User_Role  |  Role  |

通常這種情況都會再開一張表來存放兩張表間的關聯，

但更新的時候都需要

```
$user_role = SELECT * FROM  User_Role WHERE u_id = 1

for .....
    判斷哪個條件要刪除
    哪個條件要被新增
```

光想到這段程式，就覺得要一兩個if來協助完成，

在laravel的relationships有著三種語法可以協助完成這件事情，

當然拉也是要先設定好關聯。

{% codeblock lang:php %}
public function roles()
{
    return $this->belongsToMany(Role::class, 'user_role', 'user_id', 'role_id');
}
{% endcodeblock %}

設定好關聯後，就可以用以下方法操作。
{% codeblock lang:php %}

// 建立關聯
$roleId 必須是個 array
$user->roles()->attach($roleId);

// 如果 user_role 有其他欄位需要記載時
$user->roles()->attach($roleId, ['created_at' => $time]);

// 當要解除關聯時
$user->roles()->detach($roleId);

// 解除所有關聯時
$user->roles()->detach();

// 同步更新關聯
$user->roles()->sync([1, 2, 3]);


{% endcodeblock %}

是不是很方便又快速，

其實relationships還有很多方法，

像是```belongsTo```或```belongsToMany```的model更新資料時，也會更新父層資料表的時間戳，

使資料流更完整的呈現。

***
後記

Laravel真的提供很多方便的函式，

寫在官方文件中等著大家去挖掘，

每當要寫一些需求匙，都會去查一下是否已經有寫好的函式可以使用，

節省慢慢寫code的時間，也避免自己寫了神奇的bug出來 😂