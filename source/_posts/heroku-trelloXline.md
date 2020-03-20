---
title: heroku - 自動執行python腳本
date: 2020-03-20 13:55:23
categories:
    - devops
tags: 
     - devops
     - heroku
     - python
     - w3HexSchool
comments: true
---
還記得當初有寫個小工具 trelloXline notify，

當時寫完想找個雲端空間來做排程，

後來發現有heroku的相關文章，所以來嘗試看看
<!-- more -->

***
本文所採用的環境

1. Max OSX 10.15
2. Homebrew 2.2.5
3. heroku/7.39.0 darwin-x64 node-v12.13.0

[前置部署](https://yeeinhole.github.io/2020/03/14/heroku-python/)
[TrelloXLine](https://github.com/yeeinhole/TrelloXLine)
***

heroku這邊支援直接將github部署到heroku app中，

但為了方便設定config ((heroku console好像不太方便.... 

我們還是從本機端開始吧！

把TrelloXLine丟進前次所建立的資料夾中，且設定好ini檔後，

依舊使用git進行部署
```
git add -A
git commit -am "TrelloXLine"
git push heroku master
```
一樣會看到它在重新部署環境，
![hreoku-deploy](../../../../image/herohu-trelloXline/hreoku_deploy.png "hreoku-deploy")

完成後一樣先到heroku的console試試看能不能運行
```
python main.py
```
![heroku_console1](../../../../image/herohu-trelloXline/heroku_console1.png "heroku_console1")

***

接下來會用到heroku的addon項目，該功能需要先去建立信用卡資料才可以使用！！！！！

註冊信用卡後，會贈送450 hours，這樣就從每個月的550 hours變成 1000hours
![heroku_bill](../../../../image/herohu-trelloXline/heroku_bill1.png "heroku_bill")
![heroku_bill](../../../../image/herohu-trelloXline/heroku_bill2.png "heroku_bill")
```
// 可以看目前的時數
heroku ps -a trelloxline
```
![heroku_bill](../../../../image/herohu-trelloXline/heroku_bill3.png "heroku_bill")

本文的小工具照理說應該是不會超過拉～～  下個月再來更新個使用結果～～
***

接著到Resouces頁籤中 新增一個 Heroku Scheduler 的addon，
![heroku_addon1](../../../../image/herohu-trelloXline/heroku_addon1.png "heroku_addon1")
在右上角搜尋 Heroku Scheduler 
![heroku_addon2](../../../../image/herohu-trelloXline/heroku_addon2.png "heroku_addon2")
![heroku_scheduler1](../../../../image/herohu-trelloXline/heroku_scheduler1.png "heroku_scheduler1")
就直接安裝他吧！
![heroku_scheduler2](../../../../image/herohu-trelloXline/heroku_scheduler2.png "heroku_scheduler2")
這裡輸入我們的app name，進行連結
![heroku_scheduler3](../../../../image/herohu-trelloXline/heroku_scheduler3.png "heroku_scheduler3")
之後回到Resouces頁籤中，就能看到Heroku Scheduler了，
![heroku_addon3](../../../../image/herohu-trelloXline/heroku_addon3.png "heroku_addon3")
點他之後就可以開始設定我們的排程摟！
![heroku_scheduler_job1](../../../../image/herohu-trelloXline/heroku_scheduler_job1.png "heroku_scheduler_job1")
create一個job，
![heroku_scheduler_job2](../../../../image/herohu-trelloXline/heroku_scheduler_job2.png "heroku_scheduler_job2")
可惡居然只能設定 每10min、每小時、每天的某個時間，

如果會介意每天執行的話，只好到程式裡頭加上時間判斷，是要執行的日期才可以跑完程式，

而且他只能設定UTC的時間 - - ，找個網站來換算[時間]](https://datetime360.com/tw/taipei-utc-time/)
![heroku_scheduler_job3](../../../../image/herohu-trelloXline/heroku_scheduler_job3.png "heroku_scheduler_job3")
這裡為了測試，我設定一個每10min的job，

接下來就是等時間到了看有沒有發訊息到line群組中了.....
![heroku_scheduler_finish](../../../../image/herohu-trelloXline/heroku_scheduler_finish.png "heroku_scheduler_finish")

すばらし～～ 

成功設定好Heroku Scheduler了，

雖然他第兩個時間似乎好像有那麼一點點不準😂

***
後記

因為這個是小工具，所以不會超過heroku的每月上限，

但實際如果跑一個月下來有點可怕的話～～

也是有個方法，做一個index去呼叫py，

不過這邊會牽扯到free機器會sleep，

據說每次執行會比較慢一些... 畢竟要先把它吵醒...

下個月再來更新一下使用的時數是如何了