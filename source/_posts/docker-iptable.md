---
title: Docekr - iptable 設定
date: 2020-03-29 16:46:06
categories:
    - devops
tags: 
     - devops
     - docker
     - CentOS 7
     - Linux
     - w3HexSchool
comments: true
---
當部署docker環境到server上，

就會發現到預設是可以允許任何連線的...
<!-- more -->
但如果server是測試機或者私人用途不希望可以被隨意的連線時，

通常在Linux上第一個想到的是iptables 或者 firewall，

以iptables來說，基本的認知就是在 INPUT Chain設定連線規則，

但... 事情沒有那麼簡單... 

剛開始無論怎麼設定，都不會有效果的... 

所以進入了無限查資料的輪迴中...

最後在docekr官方文件中有一個章節就是跟iptables有關的...

我先說明一下我的情境，為何我想要鎖定某些ip可以存取我在docker上啟動的服務。
***
1. 該server是測試機

以我的認知，測試機的環境差不多等同於正式機，

為了一些開發偵錯的方便，會開放一些錯誤訊息，

當然不希望這些錯誤訊息被知道...等

還有測試機可以對外連線，是一件非常非常奇怪得事情 ((這不是公司內部在用的嗎...  怎麼可以被連線知道 

當然這問題如果在實體主機也可以解決，但虛擬伺服器則沒辦法透過主機網路去解決這項問題

2. 莫名被FB爬蟲機器人爬

不曉得大家有沒有發現過，當自己部署一個網站至server上，

可能有串接了某些服務，就會啟動了Facebook的爬蟲機器人， ((這點不太清楚為什麼會觸發

該機器人會定時的掃描網站，如果有做錯誤的log偵測或提醒，時不時會收到404...等的錯誤訊息 
((這裡的404可能是沒有這路徑，或者該路徑其實是需要轉跳才能出現...等

3. docker的網路架構

docker預設是使用bridge的方式，讓每個container可以連線，

以mysql來說，新增一組帳號，需要設定該帳號是 % or localhost的方式進行連線，

在docker底下不能設定localhost或者是特定ip， 
((當使用bridge模式時，當然這也可以透過設定docker網路去解決

因此目前的使用都是採用 % 的方式進行連線mysql
***
綜合上述的問題，我決定設定iptables解決這些問題，

因為稍微研究這層網路問題才發現到，docekr裝在linux時會自動到iptables上開啟一個Chain，

不論當下是使用 iptables 或者 firewall...

這個題很奇怪，畢竟認知有限無法清楚了解為何會這樣...

後來乾脆直接改用iptables，畢竟認知下用firewall應該是吃不到iptables的規則...
***
設定的方式很簡單，需要先裝好iptables，

這裡就不再截iptables安裝的畫面，

基本上灌好centos7應該是firewall，先確定是否有啟動，之後再將其停用即可
```
systemctl status firewalld
systemctl stop firewalld
# 開機不要啟動 firewalld
systemctl mask firewalld
# 安裝iptables
yum install iptables-services
# 開機自動啟動 iptables
systemctl enable iptables
# 看到綠燈表示安裝成功
systemctl status iptables
```
接著就是參照docker官方的[iptables章節](https://docs.docker.com/network/iptables/)進行設定，

文件裡也寫到預設的docker iptables都在DOCKER CHAIN裡，希望各位不要去更改該CHAIN的設定，

因此給了 DOCKER-USER CHAIN這個地方，讓大家自行設定規則。

官方得教學很簡單的說明三種使用情境 ((截取自docker docs - iptables

1. 只允許某一特定的IP連線
```
iptables -I DOCKER-USER -i ext_if ! -s 192.168.1.1 -j DROP
```

2. 只允許某一網段的IP可以連線
```
iptables -I DOCKER-USER -i ext_if ! -s 192.168.1.0/24 -j DROP
```

3. 只允許某一範圍的IP可以連線
```
iptables -I DOCKER-USER -m iprange -i ext_if ! --src-range 192.168.1.1-192.168.1.3 -j DROP
```

至於路由器模式的話，因為沒使用過，暫時不知道指令的內容會設定到什麼...

基本上指令的架構來自於iptables，這邊不多做些解釋，

唯一要注意的是... 也是讓我卡最久的地方 
((沒有注意到這個參數... 然後沒去改且跟著官方的設定只修改ip的地方... 

### ext_if 

就是它！！！ 這裡要把它改成你的網卡名稱，就是ifconfig裡對外連線的那個網卡名稱...

因此我的方法如下，當時也不太確定 ! -s 能否順利執行，因此採用所學的觀念，先拒絕全部再逐一開放可連線的IP
```
# 先拒絕所有連線
iptables -I DOCKER-USER -i eth0 -j DROP
# 允許的IP
iptables -I DOCKER-USER -i eth0 -s 000.000.00.000 -j ACCEPT
# 允許一個範圍的ip連線 ((這組ip是綠界金流的ip，API文件只有寫幾組，但我偷懶都直接開給他了... 
iptables -I DOCKER-USER -i eth0 -m iprange --src-range 175.99.72.1-175.99.72.32 -j ACCEPT
```
以上的指令去開放公司的IP或者合作夥伴的IP，

達到只允許某些IP連線至server上使用docker服務。

***
後記

docker這工具真的很方便的部署出一個環境，

但相對的沒有多花時間去研究了解他的架構，就不知道安全性上是否有疑慮，或者需要自行設定些規則...

雖然目前只部署到測試機上，還可以透過這些設定拒絕大部分連線，

至於部署到正式機上，我覺得又是另一回事了...

