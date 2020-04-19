---
title: Mac - 用zsh就是要自訂自己喜歡的樣式
date: 2020-04-03 16:51:28
categories:
    - OS X
tags: 
     - OS X
     - zsh
     - treminal
     - w3HexSchool
---
在某一次OS X更新之後，

打開終端機時我就發現，多了一串文字說明...
<!-- more -->
起初，沒有去暸解那段文字在說明些什麼，

畢竟我在終端機的使用上沒有太大的環境問題，

某天心血來朝，看一下他到底在說些什麼，
![bash](../../../../image/zsh/bash1.png "bash")
有提供個網址，來去看看...
![apple_info](../../../../image/zsh/apple_info.png "apple_info")
什麼！！！ 居然說要改用zsh作為預設的shell，

在之前看過一些文章，zsh裝了theme還有一些套件後，可以很酷的使用終端機，

無奈看到要設定一些東西，我就懶懶的，畢竟要讓zsh去抓設定在bash裡的東西...

既然官方都這樣說了，那就... 換！！！

***
本文所採用的環境

1. Max OSX 10.15
2. Homebrew 2.2.5
***
往下滑，官方也很貼心的附上教學，

先到設定裡打開 使用者 & 群組，

並點左下角的鎖頭，去解鎖之後才能設定東西，
![change1](../../../../image/zsh/change1.png "change1")
之後對自身的使用者右鍵，就會出現進階設定選單，也就是官網的那個頁面，
![change2](../../../../image/zsh/change2.png "change2")
打開選單後，在shell那邊選擇zsh
![change3](../../../../image/zsh/change3.png "change3")
之後重開終端機，就切換至zsh了！!!
![zsh1](../../../../image/zsh/zsh1.png "zsh1")
其實切換很簡單，這不是重點，重點在裝完主題後，就會出現紅框的資訊，

這邊我參考一篇之前收錄的文章 - [超簡單！十分鐘打造漂亮又好用的 zsh command line 環境](https://medium.com/statementdog-engineering/prettify-your-zsh-command-line-prompt-3ca2acc967f)，

除了安裝zsh的部分跳過外，我也跟著安裝itrem2，

該篇文章也是採用Homebrew去安裝iterm2，

大致上的設定可以參考這篇文章進行，所以我就不再說明安裝的過程，

參考文章內的流程如下
1. 安裝iterm2
2. 安裝powerline font
3. 安裝oh-my-zsh
4. 安裝powerlevel9k

其中我遇到的問題是... powerline font這邊，我無法順利的安裝，

所以突發奇想的我，去挖出了他的otf檔，[powerline](https://github.com/powerline/powerline/tree/develop/font)

直接載下來點擊安裝到mac的字體庫中，

接著就是參照說明打開 itren2或者treminal的設定
![itren1](../../../../image/zsh/itren1.png "itren1")
![terminal1](../../../../image/zsh/terminal1.png "terminal1")
這樣就可以選字體拉～～

其餘主題什麼的參照該篇文章沒有太大的問題，

設定完成後就可以讓終端機看起來更高級拉～

itren2的樣式
![itren2](../../../../image/zsh/itren2.png "itren2")
原本的terminal
![terminal2](../../../../image/zsh/terminal2.png "terminal2")
vscode也有，但我不知道要去哪改字體＠＠
![vscode](../../../../image/zsh/vscode.png "vscode")

題外話～  

我改用zsh的時候，沒有設定什麼，大部分的指令都可以使用，

像是 docker-compose , composer ...等

但也是有朋友遇到要再設定一下，

若有遇到該問題可以[參考這篇](http://to-u.xyz/2016/08/07/zsh-bash/)

是說Apple在說明改用zsh為預設的shell那頁也有寫到這點呢...
[Apple 改用zsh為預設的shell](https://support.apple.com/en-us/HT208050)

***
後記

有稍微查過 zsh 與 bash的差異，

似乎沒有太大的差異，有些指令zsh快，有些則是bash，
((似乎大部分都是zsh比較快呢！

也在某些文章的留言看到說，

使用zsh並不是因為比bash快很多，而是因為他有強大的自訂功能，

讓終端機看起來很厲害，似乎就是主題套件powerlevel9k名稱的由來呢 ＸＤ



 
