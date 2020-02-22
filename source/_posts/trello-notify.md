---
title: Trello - 寫文章也不忘用專案管理工具管理一下
date: 2020-02-22 18:56:03
categories:
    - python
tags: 
     - python
     - line
     - line notify
     - trello
     - w3HexSchool
---

大家安安～ 

不曉得大家會不會忙到差點忘了寫文章，

所以本週來玩點不一樣的～～

<!-- more -->
我們是一群人一起挑戰六角的活動，

所以我們先開了Trello來紀錄每週的主題，

但後來發現～ 沒有設定卡片的due day其實不會收到通知，

就會差點忘記寫文章，變得非常的趕～～～

正當我要一張一張設定due day時，

我突然想到為什麼不能讓它自己建呢！？ 我們不是工程師嗎！？

這種事情當然是交給程式自己來啊！！！！！！

所以我打開trello的api看一下，
![trello_api1](../../../../image/trello_notify/trello_api1.png "trello_api1")
摁～～ 有取得也有新增卡片的方法～

接著再看看list的部分也有～

那就選擇一個語言後動手開始做吧！

***
我比較習慣使用python，所以本文使用python3～

本文所採用的環境

1. Max OSX 10.15
2. Python 3.7.3

本文使用vs code的終端 或 mac終端執行py，也可以自行選擇jupyter

python套件
1. requests
2. json
3. configparser
4. datetime
5. time
6. sys
***

首先我們先到[Trello Developer](https://developers.trello.com/)，註冊一個開發者帳號，

註冊流程我就不多加說明了，接著就是申請key[api token](https://trello.com/app-key)還有token，
![trello_api_key](../../../../image/trello_notify/trello_api_key.png "trello_api_key")
token的部分在紅色的框框裡，

如果打開token的連結是401，需要先確定trello是否有登入喔！！

這邊會說你的trello有幾個board，之後都可以存取這些board，
![trello_api_token1](../../../../image/trello_notify/trello_api_token1.png "trello_api_token1")
allow之後就會出現token拉
![trello_api_token2](../../../../image/trello_notify/trello_api_token2.png "trello_api_token2")

備齊了這兩項key，我們就可以開始拉～
***
首先先開個ini檔，將剛剛拿到的key & token & board_id 放到裡面，並存成congif.ini
```
[Trello]
;trello token and key
;register key and token https://trello.com/app-key
key = key
token = token
board = board_id
;how many users in trello board
list_name = user1, user2
[Url]
;Trello
get_border = https://api.trello.com/1/boards/
add_list = https://api.trello.com/1/lists
add_card = https://api.trello.com/1/cards
```
這邊的board_id先存url上的
![board_id](../../../../image/trello_notify/board_id.png "board_id")
因為trello的卡片是需要一個list去存放的，

所以有個list名稱的設定，之後程式會split後，得到一份名稱的list，

至於url的部分，我也習慣先放進來，

接著建立一支py吧～～

以下是本文會用到的package，通通都import進來！
{% codeblock lang:python %}
# python http的套件
import requests
# 解析json
import json
# 日期時間
import datetime as dt
# 讀ini檔
import configparser
# 如果有意外我想要中斷程式
import sys
# 紀錄程式的運行時間，jupyter的話就不需要用time去計算
import time
{% endcodeblock %}
我習慣紀錄一下py的運行時間，所以先存一個啟動時間，再將ini讀進來，
{% codeblock lang:python %}
#啟動時間
start_time = time.time()

#使用ConfigParser套件讀入組態檔
conf = configparser.ConfigParser()
#載入組態檔
conf.read("config.ini", encoding="utf-8")

#Trello Key & Token
key = conf.get('Trello' , 'key')
token = conf.get('Trello' , 'token')
board = conf.get('Trello' , 'board')
list_name = conf.get('Trello' , 'list_name').split(',')

url_get_border = conf.get('Url' , 'get_border')+board
url_add_list = conf.get('Url' , 'add_list')
url_add_card = conf.get('Url' , 'add_card')
{% endcodeblock %}
以上初始化完後，就準備進入我們這次的主題

預計的步驟是
1. 新增list
2. 新增卡片並加上due date

讓我們先看到API文件上說明list的部分，
![api_list](../../../../image/trello_notify/api_list.png "api_list")
貼心的trello提供多種語言的使用方法，讓我們馬上開始吧！

這時我發現一個問題，我照著trello給的程式碼測試了一次，我卻得到錯誤訊息！！
```
>>> body = {
...     "name":"test",
...     "idBoard":board,
...     "key":key,
...     "token":token
... }
>>> response = requests.request("POST", url_add_list, params=body)
>>> print(response.text)
invalid value for idBoard
```
なにごんれ !!!!!　

這裡我也是卡了一下，我一直以為api上說要填的id是board url上的，

後來仔細看了一下說明才發現他說是 long ID

所以我們需要先讀取到board，拿到比較長的board_id，

{% codeblock lang:python %}
body = {
    "actions":"all",
    "key":key,
    "token":token
}

req = requests.request("GET", url_get_border, params=body)

# 加上判斷，如果沒有拿到http 200就報錯且離開
if not req.ok:
    print("Get botder error, http code="+str(req.status_code)+", error msg="+req.text)
    sys.exit()

board_info = json.loads(req.text)
board_id = board_info['id']
{% endcodeblock %}
拿到board_id之後，我們再測試新增一次list
```
>>> response = requests.request("POST", url_add_list, params=body)
>>> print(response.text)
{"id":"list_id","name":"test","closed":false,"idBoard":"board_id","pos":16384,"limits":{}}
```
到trello上查看，的確多了一個list
![add_list1](../../../../image/trello_notify/add_list1.png "add_list1")

那我們的目的是，有多個成員在同一個board底下，

所以使用一個迴圈去新增多個list，將trello提供的程式碼稍微修改一下，
{% codeblock lang:python %}
for i in list_name:
    body = {
        "name":i,
        "idBoard":board_id,
        "key":key,
        "token":token
    }
    req = requests.request("POST", url_add_list, params=body)

    if not req.ok:
        print("Add list error, http code="+str(req.status_code)+", error msg="+req.text)
        sys.exit()
{% endcodeblock %}
在執行一次～ 程式沒有報錯～ 到trello上檢查
![add_list2](../../../../image/trello_notify/add_list2.png "add_list2")
走到這裡非常完美～ 剩下最後一步，就可以完成任務拉

一樣看一下add card的api，也是提供了非常棒的範例，

這裡廢話不多說，就直接修改成我們想要的樣子～～

{% codeblock lang:python %}
    # 接續在新增list的迴圈中
    
    # 待會算出每週日要用到的
    sevenday = dt.timedelta(days = 7)
    # 活動開始日
    startday = dt.datetime(2020, 2, 9, 0, 0)
    # 我沒有特別查一下六角的日期，我們團隊大概預期會寫到這一週
    finalday = dt.datetime(2021, 1, 4, 0, 0)
    # 也可以將這三個變數放到迴圈外，因為他們不用每次都初始化

    # 初始化日期與數量
    day = startday
    count = 1
    while day < finalday:

        querystring = {
            "idList":new_list_id,
            "name":"##"+str(count),
            "keepFromSource":"all",
            "due":day,
            "key":key,
            "token":token
        }

        req = requests.request("POST", url_add_card, params=querystring)

        if not req.ok:
            print("Add cards error, http code="+str(req.status_code)+", error msg="+req.text)
            sys.exit()

        day = day+sevenday
        count+=1

# 最後結尾加上一個print，告知我們執行成功且花費多少時間
print("Job Success!! "+str(time.time() - start_time))
{% endcodeblock %}
確定日期都沒問題之後就讓它～ 累🐶！！
```
Yis-MacBook-Pro:trello_line_notify yi$ /usr/bin/python3 /Users/yi/python/trello_line_notify/trello_add_card.py
Job Success!! 58.967628955841064
Yis-MacBook-Pro:trello_line_notify yi$
```
一樣趕快到trello上看看有沒有出現
![add_list&card](../../../../image/trello_notify/add_list&card.png "add_list&card")
すばらし～～  

其實在等待過程中，可以盯著board看，其實蠻療癒的～
![add_list&card](../../../../image/trello_notify/add_list&card.gif "add_list&card")

***
後記

因為懶惰寫了個小工具來建立卡片跟due day，

但其實trello的提醒其實是不夠的，

所以下一週我們要加上line的notify功能，

一樣用python配合排程，在每週固定的時間挑出還沒完成文章的人，並在群組發送通知～～

一來當事人可以看到，其他團隊成員也可以互相提醒摟！

另外～～～

其實當user數量變多時，用單執行緒的方式去執行迴圈是很慢的，

這部分如果會使用多執行緒，也可以改寫成多執行緒的方式來執行，

日後再來寫一篇跟python多執行緒相關的文章吧！！

