---
title: Laradock - 設定host Apache 與 Nginx
date: 2020-02-13 23:37:34
categories:
    - devops
tags: 
     - apache
     - nginx
     - docker
     - laradock
     - devops
     - w3HexSchool
comments: true
---

Hi 大家好～ 在上篇我們初始化了Laradock也建置了個Laravel專案，

本篇要來設定，

讓Laravel用起來更優雅～

<!-- more -->
***

本文所採用的環境

1. Max OSX 10.15
2. Docker version 19.03.5

已知設定略同的環境
1. Centos7

須先了解基礎linux與vim

***

首先先設定我們本機的host

```
sudo vim /etc/hosts
```
記得加上sudo，因為hosts在 "/" 裡面，

![hosts](../../../../image/laradock2/hosts.png "hosts")

左邊是目的地IP右邊是domain
```
#IP Domain
127.0.0.1 com.laravel
```
因為要連到本機上，所以ip為127.0.0.1，並給上專案一個domain，完成後儲存且離開，

這個domain只會在本機端生效，似乎也要避免到熱門的domain，

完成後就要來設定我們的Apache與Nginx摟～

***
在Laradock中的Apache與Nginx都有一個sites的資料夾，

裡頭放的分別是
1. Apache virtualhost
2. Nginx config

首先我們先設定Apache，

到apache2/sites底下會發現有預設的檔案，我們可以複製一份新的，也可以直接修改他，

我選擇直接修改預設的檔案，
```
cd ~/dock/laradock
vim apache2/sites/default.apache.conf
```
![apache_host1](../../../../image/laradock2/apache_host1.png "apache_host1")
預設值裡已經有 ServerName 欄位了，

將後面的domain改成剛剛在hosts裡設定的
![apache_host2](../../../../image/laradock2/apache_host2.png "apache_host2")

設定完成後一定要重啟一次apache讓設定生效
```
docker-compose restart apache2
```

接著打開瀏覽器並輸入上剛剛所設定的domain，
![apache_browser](../../../../image/laradock2/apache_browser.png "apache_browser")

第一次輸入本地端的domain一定要在結尾加上 "/"，

否則會變成搜尋這個domain，

那當然會得到找不到頁面的畫面拉～～
***

以下是Nginx的部分

一樣我們先到 ngins/sites 底下，一樣也已經有預設檔案了，我們一樣直接做修改
```
cd ~/dock/laradock
cp apache2/sites/sample.conf.example apache2/sites/sample.conf
vim apache2/sites/default.conf
```
![nginx_conf1](../../../../image/laradock2/nginx_conf1.png "nginx_conf1")

一樣修改server name 與 檔案路徑，
![nginx_conf2](../../../../image/laradock2/nginx_conf2.png "nginx_conf2")

完成之後一樣先重啟nginx再打開瀏覽器試試看，
![nginx_browser](../../../../image/laradock2/nginx_browser.png "nginx_browser")

這裡會發現我是使用8888 port，因為某些關係，我把nginx改成8888port，

讓我可以同時使用apache與nginx，至於這邊改天再寫一篇吧！

***

後記

以前自己裝apache或nginx時，都需要設定這些部分，

時常忘記他們被放在哪個資料夾中，

docker的這種掛載檔案的方式，使我們很方便去修改這些地方，

這篇教學只有簡單的說明該如何使用它們，

一些進階的設定日後有機會在整理成文章分享出來。
