---
title: Heroku - 快速部署python
date: 2020-03-14 16:21:54
categories:
    - devops
tags: 
     - devops
     - heroku
     - python
     - w3HexSchool
comments: true
---

當開發好小腳本或小工具時，

我們可以來使用Heroku來建置執行的環境～～ 

<!-- more -->

為什麼選擇Heroku，

因為他目前支援的語言也蠻多的，建置過程中也不會太麻煩，

類似Git的方法就能部署，也可以串接github來自動部署。

***
本文所採用的環境

1. Max OSX 10.15
2. Homebrew 2.2.5
3. heroku/7.39.0 darwin-x64 node-v12.13.0

本次沒有測過其他平台，windows官方有提供cli工具，至於linux大概也是裝指令吧！？

準備好一個heroku的帳號

***

首先我們先來裝heroku的指令
![heroku_install](../../../../image/herohu-python/heroku_install1.png "heroku_install")
這裡我一樣使用brew來安裝～ 讓子彈飛一下
![heroku_install](../../../../image/herohu-python/heroku_install2.png "heroku_install")
最後出現了brew的酒杯，順利完成安裝

接著用treminal登入heroku吧！
![heroku_login](../../../../image/herohu-python/heroku_login1.png "heroku_login")
照著指令的提示，按下任意鍵就會跳出瀏覽器(我以為跟git一樣可以在treminal登入呢！)
![heroku_login](../../../../image/herohu-python/heroku_login2.png "heroku_login")
![heroku_login](../../../../image/herohu-python/heroku_login3.png "heroku_login")
![heroku_login](../../../../image/herohu-python/heroku_login4.png "heroku_login")
照著上面的流程後，就可以在treminal中順利的登入heroku了！

這樣heroku的指令都會對應到heroku的帳號中，

讓我們來開一個APP吧，可以用```heroku create```也可以登入到heroku建立，

這裡我就到heroku中建立吧！
![heroku_cerate](../../../../image/herohu-python/heroku_cerate.png "heroku_cerate")
建立完成後，到setting頁籤中找到Buildpacks的部分，

這裡也是可以用指令去做，既然我們已經在後台了，就直接在這邊設定吧！

Buildpacks我的理解是，設定app的語言，之後會在建立相關的檔案，讓app的環境建立起來
![heroku_buildpacks](../../../../image/herohu-python/heroku_buildpacks1.png "heroku_buildpacks")
我們這裡要用python，所以選擇python
![heroku_buildpacks](../../../../image/herohu-python/heroku_buildpacks2.png "heroku_buildpacks")
完成這些設定後，接著建置該app需要的環境，

heroku的部署會使用到git指令，
```
mkdir trelloxline
cd trelloxline
git init
git add -A
git commit -am "Heroku"
// 這裡的指令背後跟 git remote add ... 一樣，只是透過heroku指令去抓
heroku git:remote -a trelloxline 
```
到這邊我想跟著官方的指令，應該會下```git push heroku master```，

但... 事實沒那麼簡單，因為是第一次部署，我們需要先建立一個檔案，讓heroku幫我們部署App的環境，
```
pip3 freeze > requirements.txt
```
將我們本機端開發所使用到的套件...等都丟到```requirements.txt```這個檔案中，

接著我們就可以進行部署拉～
```
git push heroku master
```
![heroku_git_push](../../../../image/herohu-python/heroku_git_push.png "heroku_git_push")
登登登～ 這樣heroku就會幫我們初始化App，
(
為了測試環境是否成功，我們先建立個hello.py這個檔案，裡頭就寫個 ``` print('Hello') ```

然後一樣git add ..... ((省略git 步驟

接著回到heroku的介面中，
![heroku_index](../../../../image/herohu-python/heroku_index.png "heroku_index")
右上角這個more裡中，有終端機指令的選項，我們來測試看看檔案有沒有部署上去，
![heroku_console](../../../../image/herohu-python/heroku_console1.png "heroku_console")
老樣子先來個ls -al
![heroku_console](../../../../image/herohu-python/heroku_console2.png "heroku_console")
檔案都有順利上去，接著就是嘗試執行py了，```python hello.py```
![heroku_console](../../../../image/herohu-python/heroku_console3.png "heroku_console")

すばらし～～ 

這樣就可以部署自己做的py工具摟！！！

***
後記

之前上課的時候，就有用過heroku這工具，映像中當時git push就可以部署一個html or php，

沒想到現在部署變得比較小麻煩些，不過可能也是因為要執行python，

heroku必須知道本機端是用哪個版本的py、pip跟package，

在部署過程中爬了一下文才綜合出解決的方法 @@