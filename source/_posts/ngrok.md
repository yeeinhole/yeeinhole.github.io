---
title: Ngrok - 再也不用擔心開發遇到Webhook
date: 2020-03-08 16:32:06
categories:
    - devops
tags: 
     - ngrok
     - devops
     - laradock
     - w3HexSchool
comments: true
---

想必大家在開發成中都會遇到一些Webhook需要https才能測試的情況，

例如：Line liff的開發，金流串接導向回程式中...等

<!-- more -->
所以～ 有套工具叫做 [Ngrok](https://ngrok.com/)

***
本文所採用的環境

1. Max OSX 10.15
2. Homebrew 2.2.5
3. Docker version 19.03.5
4. laradock

已知設定略同的環境
1. Centos7
2. win10

***

在 mac 跟 linux中，都可以選擇用brew & apt ... 的指令去下載 ngrok，

我是在mac中使用Homebrew這套工具來幫我管理一些工具，他有點類似apt、yum這樣，

不過普遍的使用方法就是直接到官網，將[Ngrok](https://ngrok.com/)下載下來，

進到官網登入個帳號後，就可以下載它，
![ngrok](../../../../image/ngrok/ngrok.png "ngrok")
其實在下載頁面，官網也把使用方法寫得很詳細了，

下載解壓後，用終端機進入到檔案的資料夾中，

```
# 官方下載的檔案
./ngrok http 80

# homebrew安裝的
ngrok http 80
```
![ngrok2](../../../../image/ngrok/ngrok2.png "ngrok2")
看到出現這個畫面就表示已經成功啟動摟～～

打開瀏覽器試試看，
![ngrok_success](../../../../image/ngrok/ngrok_success.png "ngrok_success")
It' work!!

不過ngrok有一個缺點就是～ 每次啟動都會有不一樣的domain，

這樣就變得每次都要去設定apache conf 或 nginx conf，

所以我們可以設定成這樣，

```
- apache
ServerAlias *.ngrok.io

- nginx
server_name  *.ngrok.io;
```
apache的
![apache_conf](../../../../image/ngrok/apache_conf.png "apache_conf")
nginx的
![nginx_conf](../../../../image/ngrok/nginx_conf.png "nginx_conf")
之後就可以直接用ngrok給的domain進入到專案中摟！
![browser](../../../../image/ngrok/browser.png "browser")

***

後記

有了這項工具後遇到要webhook的情況就不需要找機器或者固定ip...等，

直接無痛在本機端繼續開發&測試～～

