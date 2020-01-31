---
title: Laradock - 更快速優雅的建立Laravel
date: 2020-01-27 17:35:51
categories:
    - devops
tags: 
     - laravel
     - docker
     - laradock
     - devops
---

嗨～ 大家好～ 先跟大家拜個年～  新年快樂

要來跟大家介紹的是Laradock，
<!-- more -->

Laradock是一套很方便建立Laravel環境的工具，

它採用的是Docker並且收錄了很多的工具，

像是常見的 Apache、Nginx、Phpmyadmin...等

甚至連Gitlab都有了，

功能非常的齊全，所以也有點肥大！？！？

話不多說我們開始吧～～

***

本文所採用的環境

1. Max OSX 10.15
2. Docker version 19.03.5

已知可用的環境
1. Centos7

***

首先先到Laradock的官網下載整個包 [Laradock](https://laradock.io/)，

```
git clone https://github.com/Laradock/laradock.git
```

接著複製env.example存成.env

```
cp env-example .env
vim .env
```

打開.env後發現有很多設定，

跟環境比較有關係的是這些

![.env](../../../../image/laradock1/env.png ".env")


```
### Paths #################################################
# Point to the path of your applications code on your host
# 專案要放在本機的哪個資料夾中
APP_CODE_PATH_HOST=./www/

# Point to where the `APP_CODE_PATH_HOST` should be in the container
# 在container中，專案的資料夾名稱，這裡會跟上面的地方做對應
APP_CODE_PATH_CONTAINER=/var/www

# You may add flags to the path `:cached`, `:delegated`. When using Docker Sync add `:nocopy`
# 還不知道可以做什麼.....
APP_CODE_CONTAINER_FLAG=:cached

# Choose storage path on your machine. For all storage systems
# 系統檔案的路徑，有些工具會有一些非專案的檔案，就會存放在此
DATA_PATH_HOST=~/.laradock/data
```

除了基本的環境設定外，

.env檔裡存放著各個工具的預設帳號密碼與相關設定，

只要修改欲使用工具的設定值即可

，在此以phpmyadmin做演示，

![.env-phpmyadmin](../../../../image/laradock1/env-phpmyadmin.png ".env-phpmyadmin")

```
# Accepted values: mariadb - mysql
# phpmyadmin搭配的sql是誰

PMA_DB_ENGINE=mysql
```

注意者裡一定要設定，

屆時開啟phpmyadmin就會自動啟動sql

```
# phpmyadmin的預設使用者
PMA_USER=default

# 預設使用者的密碼
PMA_PASSWORD=secret

# sql root帳號的密碼
PMA_ROOT_PASSWORD=secret

# phpmyadmin要開在哪一個port
PMA_PORT=8080
```

這邊要注意的是root的密碼，

預設使用者的密碼可以屆時再改或設定成亂碼，

畢竟這些預設值都是眾人皆知的，

以資安來說最好是採用亂數密碼來設定比較好，

以上設定完了之後就可以來啟動拉～

```
docker-composer up -d apache phpmyadmin 想啟動的服務...
```

在這裡我只跟docker說我要啟動 apache 和 phpmyadmin 這兩項服務，

接著你會發現docker會啟動數個container，

![docker-start](../../../../image/laradock1/docker-start.png "docker-start")

仔細觀察可以發現，

他將workspace、php、apache、mysql都分開來，

好處就是今天更換其中一個的版本，

都不需要額外設定主機的環境變數...等，

這些事情docker都幫你在底層處理好了～～ 

接著來看一下container的狀態

```
docker ps 
或
docker-compose ps
```
![docker ps](../../../../image/laradock1/docker-ps.png "docker ps")

可以看到每個container開啟的port號與狀態，

所有的服務都有順利執行，

我們打開瀏覽器並輸入localhost:8080，來檢查phpmyadmin是否順利啟動

![phpmyadmin](../../../../image/laradock1/phpmyadmin.png "phpmyadmin")

```
Server = mysql or mariadb
Username = default
Password = secret
```

就可順利進入到phpmyadmin的主畫面了
![phpmyadmin index](../../../../image/laradock1/phpmyadmin-index.png "phpmyadmin index")

接下來就是建立專案拉～

先進入到workspace這個container中，

會發現預設進入的資料夾是 /var/www/，

```
docker-compose exec workspace bash
composer create-project --prefer-dist laravel/laravel myproject
vim .env
```

將DB連線設定為 mysql or mariadb並設定sql的使用者與密碼

```
DB_HOST=mysql 
DB_PORT=3306
DB_DATABASE=default
DB_USERNAME=default
DB_PASSWORD=secret
```

因為在docker裡container與container之間是用名稱去做連結，

不同以往採用localhost的方式連線sql

設定完成後打開瀏覽器輸入 localhost/myproject/public，

如果出現laravel的預設畫面，表示我們已經順利將專案建立起來拉～～

![Laravel index](../../../../image/laradock1/laravel.png "Laravel index")

***

後記

這裡我比較偷懶，沒有額外去設定vitrulhost，安全性也會比較有疑慮，

如果用nginx則需要再設定conf與host才能順利執行，

改天在寫一篇設定vitruhost 跟 nginx conf的教學 ～～

