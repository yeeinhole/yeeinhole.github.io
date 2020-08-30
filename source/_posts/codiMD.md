---
title: 建立團隊專屬的CodiMD
date: 2020-08-30 23:06:41
categories:
    - devops
tags: 
     - devops
     - docker
     - w3HexSchool
comments: true
---
無意間發現團隊成員有在使用HackMD紀錄筆記，

後來想說直接開一個group，

結果... 免費的人數只有3位呀～～～～～～  T_T
<!-- more -->
***
本文所採用的環境

1. Max OSX 10.15
2. Docker version 19.03.5
***

然後就發現了CodiMD，其實就是HackMd的open版，

需要自行架設與維護，

不過，

官方也是很貼心得整理各種部署的方法，

本文採用 docker 的方法進行架設，

這邊有一篇分析 [HackMD Business v.s. CodiMD得差異](https://hackmd.io/@docs/hackmd-vs-codimd)

可以看過這篇後再決定怎麼建置。
***
我們直接來看看CodiMD提供的docker composer吧

[Docker Deployment](https://hackmd.io/c/codimd-documentation/%2Fs%2Fcodimd-docker-deployment)

官方說明很簡單的說了，直接把內文製作成一份docker-compose.yml，

這邊要注意的是，記得修改資料庫的帳號與密碼，

也就是```POSTGRES_USER```和```POSTGRES_PASSWORD```，

特別注意```CMD_DB_URL=postgres://codimd:change_password@database/codimd``` 這段的 ```codimd:change_password``` 也要記得修改

database的部分完成後，接著設定CodiMD圖檔上傳的位置，

```
// upload-dat 請寫上主機上存放CodiMD檔案的位置
volumes:
      - upload-data:/home/hackmd/app/public/uploads
```

修改好位置後，記得將資料夾的權限設為 uid 1500、gid 1500 的使用者權限與群組，

然後看想把服務開在哪一個 port 上，這邊預設是 3000 port，

以上建置好了之後，就直接啟動它吧 ```docker-compose up -d```

啟動後 3000 port 應該就可以看到 CodiMD的主頁了，

![codimd](../../../../image/codimd/codimd1.png "codimd")
預設是可以看到 login，不過因為內部使用，我就把它給關掉了

關掉後該怎麼註冊呢！？

登入進 container，

```ls -al```一下bin這個資料夾，
![codimd2](../../../../image/codimd/codimd2.png "codimd2")

manage_users 就是管理使用者的指令集，

對他 ```--help``` 你就會發現
![codimd3](../../../../image/codimd/codimd3.png "codimd3")

他提供 新增、刪除、重置密碼、最後一個```--pass```沒有很懂意思ＸＤ，

畢竟這是open source版本，不像HackMD提供完善的管理介面，

對於小團隊來說已經足夠了～～
***
Ref
1. [Docker Deployment](https://hackmd.io/c/codimd-documentation/%2Fs%2Fcodimd-docker-deployment)
2. [辛西亞的技能樹 -【CodiMD】安裝踩雷筆記...](https://cynthiachuang.github.io/How-to-Setup-CodiMD/)
3. [HackMD Business v.s. CodiMD得差異](https://hackmd.io/@docs/hackmd-vs-codimd)
***

後記

團隊中一直都沒有共享資源的平台，大部分都使用gsuit，

雖然可以達到共享，不過程式碼的紀錄，就不能很順暢的排版，

又沒辦法推hexo，畢竟這比較偏向個人blog，

後來帶領的新成員會用HackMD將我所教的記錄下來，

因此讓我想推動markdown的平台～～～

所以找個時間點將這平台給架設完成。