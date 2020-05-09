---
title: Laravel之旅 - 生成Migration的懶人包
date: 2020-05-09 21:00:38
categories:
    - Laravel
tags: 
     - laravel
     - w3HexSchool
comments: true
---
在前一週，我們介紹了migration的用法與好處，

但... 

舊有的專案有好幾十張表，該不會需要一一建立吧...
<!-- more -->
當然是不用，

有工具可以將sql檔，

輸出成laravel的migration檔案，

需要安裝 mysql workbanch！

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
***

還沒安裝的請點這裡～～ [Mysql Workbanch](https://www.mysql.com/products/workbench/)

裝好的我們就開始吧！！！

先到這個github下載外掛 [MySQL Workbench Export Laravel 5 Migrations Plugin](https://github.com/beckenrode/mysql-workbench-export-laravel-5-migrations)

這個外掛是用py寫的，至於版本作者並未說明，

下載好了之後，我們打開Mysql Workbanch，

找到安裝外掛的地方，
![mysql workbanch1](../../../../image/laravel-2-1/w1.png "mysql workbanch1")
選擇剛剛下載的py，
![mysql workbanch2](../../../../image/laravel-2-1/w2.png "mysql workbanch2")
等他安裝一下，接著會跟你說裝好了，
![mysql workbanch3](../../../../image/laravel-2-1/w3.png "mysql workbanch3")
接下來的步驟是因為平常沒有在使用mysql workbanch，

因此有以下的步驟，如果平常有在使用，則可以跳到輸出的頁面，

建立一個Model，
![mysql workbanch4](../../../../image/laravel-2-1/w4.png "mysql workbanch4")
出現工作區域的介面，並選選擇匯入，
![mysql workbanch5](../../../../image/laravel-2-1/w5.png "mysql workbanch5")
選擇sql檔，下一步到匯入完成
![mysql workbanch6](../../../../image/laravel-2-1/w6.png "mysql workbanch6")
![mysql workbanch7](../../../../image/laravel-2-1/w7.png "mysql workbanch7")
![mysql workbanch8](../../../../image/laravel-2-1/w8.png "mysql workbanch8")
完成後，就可以在工作區域看到我們所匯入的資料表了，
![mysql workbanch9](../../../../image/laravel-2-1/w9.png "mysql workbanch9")
接著我們就可以來匯出轉成migration型態的檔案拉！

到tools->catalog->Export Laravel 5 Migration
![mysql workbanch10](../../../../image/laravel-2-1/w10.png "mysql workbanch10")
可以在紅色框框中瀏覽輸出的樣子，
![mysql workbanch11](../../../../image/laravel-2-1/w11.png "mysql workbanch11")
不知道是不是mac版的關係，

選擇輸出資料夾的地方，有夠難點的，可以點底線的右邊比較容易讓視窗打開，

這裡不選擇資料夾的話，會不知道輸出到哪裡去了，我實在是找不到阿...

選完資料夾後完成即可，

到剛剛的資料夾就可以看到輸出的檔案，
![migration1](../../../../image/laravel-2-1/m1.png "migration1")
打開其中一個檔案來看一下，
![migration2](../../../../image/laravel-2-1/m2.png "migration2")
是不是跟我們上週的觀察的user很像啊～～～

有了這項工具後，可以無痛的建立舊專案的migration摟！！！

***
後記

正要做一個新的專案時，老闆將DB規劃好後丟給我，

開工的我默默地建migration，

老闆突然問說，這沒工具嗎！？

我沈默了一下... 我居然... 沒有想到要去google看看...

接著我就找到這個github了，

然後就快速的建立migration～～～

下次記得要先google看看有沒有懶人法或工具啊啊啊...