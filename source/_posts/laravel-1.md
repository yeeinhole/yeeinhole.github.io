---
title: Laravel之旅 - 認識Laravel資料夾結構
date: 2020-04-25 21:11:35
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
一直想寫一系列如何認識與上手Laravel，

自認自己的學法特殊，師父只教了如何CRUD，剩下的就由它任意發展了～～
<!-- more -->
最後決定開寫的原因是，

整理手邊的筆記 & 讓自己更加認識 Laravel，

除了基本的開發，其實還有很多很多個地方沒有用到，

就趁著個機會，多少接觸些。

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

我們先來說說Laravel的資料夾結構，


|  資料夾名稱   | 說明  |
|  ----  | ----  |
|  app   | 開發程式的地方，裡面有Controller、Exceptions... 待會再詳細說明 |
|  bootstrap  | 啟動laravel的地方，基本的開發不會改動到這區塊 |
|  config  | 設置系統參數的地方，可以定義一些文字或變數，供程式使用，改變參數就不需要改程式 |
|  database  | 存放DB的scheme與設定假資料的地方 |
|  public  | web的進入點，圖檔、js、css、編譯過的檔案都會放在這裡 |
|  resources | 前台資源的地方，編譯前的js、scss、語言檔、views |
|  router  | 路由參數，設置url對應controller的地方 |
|  storage  | 存放編譯過的blade、logs、暫存檔的地方，基本的開發不會改動到這區塊 |
|  tests  | 撰寫測試程式的地方 |
|  vendor  | package的目錄，與node_modules的用途一樣 |

|  檔案   | 說明  |
|  ----  | ----  |
|  .env .env.example  | 系統參數的檔案 |
|  composer.json .lock  | Laravel版本、已安裝的package，一些composer的設定， .lock則是編譯過後的.json檔，供部署或其他開發者，安裝相同版本的package |
|  package(-lock).json | 用npm管理js套件的地方，用途跟composer相似 |
|  phpunit.xml | 設定php unit test的檔案 |
|  server.php | 模擬一個apache或nginx的設定，可以快速建立一個測試環境來執行laravel |
|  webpack.mix.js | webpack打包js、scss的設定檔，需要搭配node和npm服用 |

以上是laravel預設給予的資料夾與檔案，

通常開發中比較常用到的是 app、config、database、resources、router、test((除非不寫測試，

app這個資料夾預設只有 Console、Exception、Http、Providers跟一個User.php，

而這些的用途如下

|  資料夾   | 說明  |
|  ----  | ----  |
|  Console | 可以用來撰寫atrisan指令與排成指令 |
|  Exceptions  | 例外處理，可以自定錯誤文字或者錯誤後該做什麼動作的地方，預設會將exception向上丟出(如果.env設置debug=true時就會呈現在畫面中)也會把錯誤記錄到log中 |
|  Http | 撰寫Controller、Middleware的地方，預設會給一套登入/註冊系統的Controller，Middelware則有常見的是否已登入、Cookie加密、CSRF白名單...等的判斷 |
|  Providers | 提供自訂provider的地方，laravel啟動時會載入許多providers來建置整個系統，providers的清單在config/app.php中，官方文件是說大部分的providers都是緩載入的情況，當被使用時才會真實載入 |
|  User.php | 是一個Eloquent ORM的Model，可以操作預設的users資料表 |

這些是預先載入的資料夾，

其實還有一些資料夾沒有載入，讓我來逐一說明其功能，

|  資料夾   | 說明  |
|  ----  | ----  |
|  Broadcasting | 顧名思義是指廣播系統，也就是跟webSocket有關係，可以讓後端程式發訊息到前端的socket，可以搭配Laravel Echo、Pusher來使用 |
|  Events | 定義事件的地方，可以說是 當...時，通常會配合listener使用 |
|  Listeners | 定義一些邏輯被觸發執行，也就是當event時做...，跟隨著event發生時做某件事情 |
|  Mail | 建置與封裝跟mail有關的程式邏輯 |
|  Notifications | 可以設定當系統觸發什麼事件就發送一個簡單的通知，官方舉例：寄mail、Slack...等 |
|  Policies | 可以寫條件規則，限制使用者的操作，可搭配Gate使用，讓router受到條件限制使用 |
|  Http\Requests(Rules) | 撰寫Form Validation的規則，讓post進來的變數只留下有定義的，也可以透過 http method 增減驗證規則 |

以上是官方文件中提到不會預設載入的功能資料夾，

那～ 應該有人會好奇說... 為什麼沒有提到Models，

官方的說明是 Model 一詞的定義較分辨不清，

原因是在於有些人定義為 "業務邏輯" 有些人定義為 "資料庫數據"，

因此laravel不預設定義Model為何，留給開發人員自行定義，

而我... 是把它定義成資料庫的那一派，

我把業務邏輯定義到Service或Helper中。

再來要說明的是程式的資料夾，

這部分我大致上看到兩種派別，

一種是依照 "系統功能" 另一種則是 "程式功能"，

其實兩者字面上的意思很相近，((我也忘記這兩種分法的正確名稱，這是我自己理解出來的名稱

以 "會員管理" 這個功能來說會有這些程式

1. Member.php (Model)
2. MemberController.php (Controller)
3. MemberService.php (Service業務邏輯)
4. ...

當 "系統功能" 做區分時資料夾結構長這樣

```
app|
   |Http
   |----|Member
   |----|------|Member.php
   |----|------|MemberController.php
   |----|------|MemberService.php
```
當 "程式功能" 做區分時資料夾結構長這樣
```
app|
   |Http|
   |----|Controller|
   |----|----------|MemberController.php
   |----|----------|OOXXController.php
   |
   |Models|
   |------|Member.php
   |------|OOXX.php
   |
   |Services|
   |------|MemberService.php
   |------|OOXXService.php
```

至於哪一種比較好，見仁見智，視專案狀況決定，

當你的專案功能數目龐大，我覺得採用 "系統功能" 會比較好，

而專案沒有那麼大的情況下，用 "程式功能" 來做區分就可以了，

那我呢... 目前是 "程式功能" 這一派的，

或許當我某天遇到什麼瓶頸時，就會更改我的作法了吧～～

今天就先說到這裡吧！！ 

***
Ref
[Laravel6.0 - directory](https://laravel.com/docs/6.x/structure#the-rules-directory)


***
後記

摁～ 透過去仔細查閱官方文件，才發現到其實還有很多功能沒有運用到，

很多時候都在Controller或Service做完這些事情，

但其實laravel早就幫你想好了，

後續在逐一嘗試一些官方所提供的功能吧！
