---
title: Laravel之旅 - DB的操作(3) Relationships
date: 2020-07-19 18:22:52
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
已經說明DB Query和ORM操作的不同，

讓我們來聊聊 ORM 厲害的地方，

就是 relationships 的關聯設定，

可以很快速的join資料。

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
***

通常我們在資料比中的關聯，要不是1對1不然就是1對多，

通常遇到這種形態的資料時，往往第一個想到的是join這個DB語法，

在Laravel ORM中，則提供了快速的方法，可以進行join撈資料，

其實方法很簡單，只要到Model中建立function，並使用對應的語法即可。
{% codeblock lang:php %}
// 回到前幾篇提到的 User.php中加入以下

use App\Post;

public function post()
{
    return $this->hasMany(Post::class);
    // 這樣寫也可以 
    // return $this->hasMany('App\Post');
}
{% endcodeblock %}

接著只要在Query的時候加上
{% codeblock lang:php %}
$post = User::where('name', 'Yee')->get()[0]->post;

// 執行get後才會從 Illuminate\Database\Eloquent\Builder 型態 轉變成 Illuminate\Database\Eloquent\Collection
// 因為還沒將資料回傳，因此不能幫每一筆資料join上其他表的資料
// 故這裡寫get()後取第一筆資料去取post
{% endcodeblock %}

參照官方文件的方法如下，這樣的方法是可以再使用 relationships 取資料
{% codeblock lang:php %}
$post = App\User::find(1)->post;

// 我在想應該是這樣的用法下回傳的是單一筆資料，因此可以使用relationships取得關聯資料
{% endcodeblock %}

如果要取得所有使用者和使用者的所有post可以用迴圈這樣做
{% codeblock lang:php %}
$user = User::get();
for($i=0; $i<count($user); $i++){
    $user[$i] = $user[$i]->post;
}

// 你也可以用 collection 或 foreach 都可以達成目的
{% endcodeblock %}

也可以用預載入的方式
{% codeblock lang:php %}
User::with('post')->get()
// User::with('post') 的時候還是屬於 Illuminate\Database\Eloquent\Builder 型態
// 但他會預載入B資料表，因此可以減少查詢的次數
{% endcodeblock %}

這樣就可以取得User這張表的資料，並且將每一筆資料都join上post這張表的資料，非常的方便和快速。

Relationships的部分官方文件中列了以下幾種方式
1. One To One , hasOne()
2. One To Many , hasMany()
3. One To Many 逆向 , 即以B表取得A資料表, belongsTo()
4. Many To Many , belongsToMany()

上面的這些寫法都是處在於
1. A資料表的主鍵為"id"
2. B資料表的外來鍵為"tableName_id"，

因為他會自動以 "tableName_id" 作為B資料表的外來鍵，

若不符合以上情境可以這樣寫

{% codeblock lang:php %}
return $this->hasMany('App\Post', 'foreign_key', 'local_key');
// foreign_key 指的是B資料表中屬於A資料表的主鍵欄位
// local_key A資料表的主鍵欄位
{% endcodeblock %}

以上是我比較常用跟常見的操作方法，

更多方法可以看這邊[Laravel - Eloquent: Relationships](https://laravel.com/docs/6.x/eloquent-relationships)

***
後記

以前剛學laravel時，大多使用DB Query進行DB的操作，

後面接觸ORM後了解到他的快速後，大部分都使用ORM進行操作，

預載入的部分雖然比較快速，不過也是要看系統架構與情境，

只有符合情境才是最佳的操作。