---
title: Trello X Line - 再也不會忘記寫文章
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

繼上一篇先將Trello卡片開好且設上每週的due day，

但Trello的通知只有在due day前寄一封信通知，

這是不夠的啊啊啊啊啊～～

因此我要將它進化到定時通知趕快寫文章呀！！！！

<!-- more -->

***
我比較習慣使用python，所以本文還是使用python3～

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
7. line_notify
***

首先先來註冊[Line Notify](https://notify-bot.line.me/my/)，
![line_notify](../../../../image/trello_notify2/line_notify.png "line_notify")
點下Generate token，
![line_notify2](../../../../image/trello_notify2/line_notify2.png "line_notify2")
輸入上這notify的名字，

接著如果只要推播給自己請選擇 1-on-1 的選項，

如果你也和我一樣是一群人，就選擇你們的群組吧！
![line_notify3](../../../../image/trello_notify2/line_notify3.png "line_notify3")
恭喜你已經拿到token摟！

接著打開Line搜尋官方帳號 Line notify並加入好友，
![line_notify4](../../../../image/trello_notify2/line_notify4.png "line_notify4")
如果是單人就到這裡，如果是群組，請把它加到群組裡！！！
![line_notify5](../../../../image/trello_notify2/line_notify5.png "line_notify5")
***

接著打開上一篇所使用的ini檔，

把這次的line token加進去～
```
[Line]
;register token https://notify-bot.line.me/my/
line_token = your token

[Url]
;Line
url = "https://notify-api.line.me/api/notify"
```
接著開一支py吧！跟上一篇一樣的方式初始化、讀取ini...吧
{% codeblock lang:python %}
import requests
import json
import datetime as dt
import line_notify as line
import configparser
import sys

#啟動時間
start_time = time.time()

conf = configparser.ConfigParser()
conf.read("config.ini", encoding="utf-8")

#Trello Key & Token
key = conf.get('Trello' , 'key')
token = conf.get('Trello' , 'token')
board = conf.get('Trello' , 'board')

#line token
line_token = conf.get('Line' , 'line_token')
#line nptify url
url_notify = conf.get('Url' , 'notify')
{% endcodeblock %}
以上初始化完後，就準備進入我們這次的主題

預計的步驟是
1. 抓出當週誰還沒寫文章
2. 推播吵死他！！！

我們先來設計取當週週日的日期，

不過datetime只能告訴我這是禮拜幾，沒辦法直接告訴我禮拜日的日期，

所以～～～ 自己來判斷吧...
{% codeblock lang:python %}
# 先今天的日期
today = dt.datetime.now()
checkday = dt.datetime.now()
# 製作一個參數表示1天，待會拿來計算用
oneday = dt.timedelta(days = 1)

# 判斷出週日的日期
# 當今天不是禮拜日，就加1
# isoweekday() 0 & 7 都是禮拜日喔！
while checkday.isoweekday() != 7:
    checkday += oneday

# 今天距離週日還有幾天
lessday = checkday - today

# 轉成文字才能與Trello比對
checkday = checkday.strftime("%Y-%m-%d")
{% endcodeblock %}
計算完日期之後，取出卡片上的due day來做判斷，

先來看一下Trello Api，
![trello_api](../../../../image/trello_notify2/trello_api.png "trello_api")
我們要取list的name、open的card、card的due day、card的dueComplete狀態，
所以我們的query string長這樣
{% codeblock lang:python %}
# 因為要組上board id，所以這裡沒有使用ini裡的url
url_get_list = "https://api.trello.com/1/boards/"+board+"/lists"

querystring = {
    "cards":"open",
    "card_fields":["due","dueComplete"],
    "filter":"open",
    "fields":"name",
    "key":key,
    "token":token
}

# 取得board裡的卡片清單
req = requests.request('GET', url_get_list, querystring)
card_lists = json.loads(req.text)
print(card_lists)
{% endcodeblock %}
![card_list](../../../../image/trello_notify2/card_list.png "card_list")
在終端機看到密密馬馬的這串，就表示我們成功取到list的清單摟～～

接著解析它，並設計迴圈來檢查文章寫完了沒，
{% codeblock lang:python %}
finish_users = []
not_finish_users = []

# 偵測卡片的due date有沒有截止日的
for lists in card_lists:
    finish = False
    for card in lists['cards']:
        # 卡片沒設定due的話跳過這一張卡片
        if card['due'] == None:
            continue
        
        # 抓卡片的due date 跟 是否打勾
        if (checkday == card['due'].split('T')[0]) and (card['dueComplete']):
            finish = True
            break
    
    # 紀錄該週完成與未完成的人
    if not finish:
        not_finish_users.append(lists['name'])
    else:
        finish_users.append(lists['name'])
{% endcodeblock %}
我們團隊說好寫完文章的，要去把他的due day打勾，

所以我要檢查due day的狀態是否為true，

抓出誰沒完成後，最後一的動作就是發Notify啦～

在這之前先來製作要跟大家說的話，
{% codeblock lang:python %}
# 製作訊息
# \n是python的字串斷行～
message = {'message': "\n"
    "Hi 各位安安 ～ \n"+
    "--- \n"+
    "本週已完成的人： \n"+
    ("，".join(finish_users))+"\n"+
    "請下週再接再厲～～\n"+
    "--- \n"+
    "本週未完成的人： \n"+
    ("，".join(not_finish_users))+"\n"+
    "距離本周截止你只剩下"+str(lessday.days)+"天了～～ \n"+
    "趕快去寫文章！！ \n"+
    "--- \n"+
    "投稿連結： \n"+
    "https://forms.gle/MvufZiucPir4yhHb6"
}
{% endcodeblock %}
然後就是把這串message傳到line notify拉～
{% codeblock lang:python %}
headers = {
    "Authorization": "Bearer " + line_token, 
    "Content-Type" : "application/x-www-form-urlencoded"
}

req = requests.request("POST", url_notify, headers=headers, params=message)
print(req.status_code)
{% endcodeblock %}
![line_notify6](../../../../image/trello_notify2/line_notify6.png "line_notify6")
看到http200了，趕快來看看line上出現了什麼～
![notify_reseponse](../../../../image/trello_notify2/notify_reseponse.png "notify_reseponse")

すばらし～～  

看到誰沒完成，就趕快tag他，給他溫暖的關愛 ❤️❤️❤️

***
後記

因為專案的關係，了解到可以用Line Notify來做一些系統通知，

而且這服務似乎是沒有計算費用的～～

比起開一個line@來推播，可以省下一些費用，

這篇教學是透過開發者串連Notify到群組發訊息，

如果是要做在系統讓user來綁定Line Notify，

其實對於一些使用者來說是有一點操作門檻的，

各別綁定這部分沒有在這篇文章列出來，

因為那又是另外一回事了～～

有機會再來做一篇

開個小服務讓其他user到平台上綁定，並各別推播訊息到user手中

本文code的連結：產生中... XD

希望大家用的開心～ 準時寫文章～ 一起拿金質獎～
