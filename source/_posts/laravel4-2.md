---
title: Laravel之旅 - 靈活的產生美麗的畫面吧
date: 2020-09-30 18:12:15
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
能在Controller中寫寫code也串完router了，

那接下來就是該把漂亮的畫面呈現出來了！！！

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
[Laravel之旅 - 認識Laravel資料夾結構](https://yeeinhole.github.io/2020/04/25/laravel-1/)
[Laravel之旅 - 善用Migration管理資料表](https://yeeinhole.github.io/2020/04/25/laravel-2/)
[Laravel之旅 - 生成Migration的懶人包](https://yeeinhole.github.io/2020/04/25/laravel-2-2/)
[Laravel之旅 - DB的操作(1) DB Query builder](https://yeeinhole.github.io/2020/05/17/laravel-3/)
[Laravel之旅 - DB的操作(2) Eloquent ORM](https://yeeinhole.github.io/2020/05/24/laravel-3-2/)
[Laravel之旅 - DB的操作(3) Relationships](https://yeeinhole.github.io/2020/07/19/laravel-3-3/)
[Laravel之旅 - DB的操作(4) Relationships 特別篇](https://yeeinhole.github.io/2020/07/26/laravel-3-4/)
[Laravel之旅 - 在Controller中開始寫寫code摟](https://yeeinhole.github.io/2020/08/02/laravel-4/)
[Laravel之旅 - 在Controller中開始寫寫code摟](https://yeeinhole.github.io/2020/08/07/laravel-4-1/)
***
在laravel中，resource/view/這個資料夾是在放畫面檔案的部分，

laravel的blade可以將畫面切分成好幾個小塊再import進主要的畫面中，

所以我都會把設計的版切分成 main header body footer，如果左右還有選單的話也會切進去，

簡單來說main我會放主體，並寫下載入blade的標籤，
```
<html>
    <head>
        @yield('head')
    </head>
    <body>
        <div>
            @yield('content')
        </div>
        @yield('body_script')
    </body>
</html>
```
通常我會埋下這些標籤，讓子模板去使用，

將整體會使用的JS、CSS放在這裡，再額外寫上```head```跟```body_script```這兩個標籤，

讓子模板插入他們各自的JS與CSS，

```@yield``` 標籤主要是用來說明，該區塊是子模板插入的位置，

子模板只要使用section並使用這個標籤的名稱，就可以將html插入至這個區塊。

在子模板，我的blade通常都會是這樣
```
@extends('defaults.index')

@section('head')
@endsection

@section('content')
    @include('some_blade')
@endsection

@section('body_script')
@endsection
```

```@extends``` 標籤主要用來說明該模板會繼承哪一個blade，

```@section & @endsection``` 包覆起來的html最終會出現在父層blade對應的區塊中。

``` @include ``` include一個blade到這個區塊中，如果blade中有共用的部分，就可以在抽出來變成blade去載入。

通常都用這幾個標籤，官網還有```@component```...等，

在7.0開始多了 components 章節，大致上瞭解的功能有點像 vue ～～～～ 吧，

似乎可以為了這個模板獨立寫一支程式，讓畫面更容易去渲染對應的資訊之類的。

***
後記

因為開始接觸一些Vue後，比較少來研究laravel的blade，

沒想到除了上面常用的，還多了很多，這篇先介紹我常用的，

後面有機會再寫寫新的用法，

畢竟我覺得有應用在專案中，才會更了解該標籤～～ 

否則讀完跟小嘗試後還不夠有感覺～～
