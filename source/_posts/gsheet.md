---
title: Laravel - 讀寫 google sheet 
date: 2020-04-19 18:37:52
categories:
    - Laravel
tags: 
     - laravel
     - google sheet
     - w3HexSchool
comments: true
---
某天，有個需求，能否自動匯出資料到google sheet，

當然是沒問題～ 

先看看google官方的文件... 似乎有點麻煩呀，

那就看看laravel有沒有package了...
<!-- more -->
當然是有的，而且省去了官方文件上一連串登入的動作，

***
本文所採用的環境

1. Max OSX 10.15
2. Docker version 19.03.5

[Google Sheets API v4 for Laravel](https://github.com/kawax/laravel-google-sheets)
***

這package的存去方式是透過在google apis中，

開啟 google sheet 和 雲端硬碟 的api使用權限，

並建立一個虛擬帳戶，供程式使用該帳戶登入並存取api，

所以我們先到google apis中建立專案，
![api1](../../../../image/gsheet/api1.png "api1")
建立完專案後，啟動api服務，
![api2](../../../../image/gsheet/api2.png "api2")
本次要起動 sheet 和 drive 這兩項服務，
![api3](../../../../image/gsheet/api3.png "api3")
![api4](../../../../image/gsheet/api4.png "api4")
點下啟用後就完成了！((drive也是如此方式啟動

接著就是建立一個虛擬帳戶，來供程式存取，

依稀還記得以前都是直接開一個google帳號用來登入，

現在可以透過虛擬帳戶，真的是比較方便，又不用怕密碼放到程式中...

左側選單中找到api與服務，再選憑證
![api5](../../../../image/gsheet/api5.png "api5")
進入憑證的頁面後，選擇建立憑證，
![api6](../../../../image/gsheet/api6.png "api6")
輸入該虛擬帳戶的名稱，
![api7](../../../../image/gsheet/api7.png "api7")
幫該帳戶設定權限
![api8](../../../../image/gsheet/api8.png "api8")
幫憑證弄一組金鑰
![api9](../../../../image/gsheet/api9.png "api9")
右側會跳出選項，這裡我們選擇json
![api10](../../../../image/gsheet/api10.png "api10")
之後就會自動下載一個json檔，該檔案會有這個虛擬帳戶的登入資訊，請妥善保管喔！
![api11](../../../../image/gsheet/api11.png "api11")
開一個google sheet，並分享這個檔案給虛擬帳戶
![api12](../../../../image/gsheet/api12.png "api12")

***
接下來回到Laravel，

先安裝這個package，並建立組態檔
```
# 安裝package
composer require revolution/laravel-google-sheets
# 設定組態檔
php artisan vendor:publish --provider="PulkitJalan\Google\GoogleServiceProvider" --tag="config"
```

***
小貼士

package預設是給laravel >= 5.8的專案使用，若現在低於這個版本的話...

別擔心這邊有[更新紀錄](https://github.com/kawax/laravel-google-sheets/blob/master/UPGRADING.md)

該package還有支援到 php7 + laravel 5.5 的呦～～ 

當時找到這package時也忘了版本，研究好也寫個service要交給同事時，才想到自家專案是5.5阿... 

差點以為要熬夜從來了，還好有來看一下，還好有支援5.5呀～～～
***

裝好將我們剛剛取得的json檔放到public底下，

然後到.env底下，將以下貼入
```
GOOGLE_APPLICATION_NAME=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT=
GOOGLE_DEVELOPER_KEY=
GOOGLE_SERVICE_ENABLED=true
GOOGLE_SERVICE_ACCOUNT_JSON_LOCATION=剛剛取得的那個json檔
POST_SPREADSHEET_ID=google sheet的 id
POST_SHEET_ID=google sheet作業簿的名字
```
POST_SPREADSHEET_ID 跟 POST_SHEET_ID 在這裡
![laravel1](../../../../image/gsheet/laravel1.png "laravel1")
之後到congfig/google.php中貼上
{% codeblock lang:php %}
# scopes 表示該token可以使用哪些權限
'scopes' => [\Google_Service_Sheets::DRIVE, \Google_Service_Sheets::SPREADSHEETS],
---
'post_spreadsheet_id' => env('POST_SPREADSHEET_ID', ''),
'post_sheet_id'       => env('POST_SHEET_ID', ''),
{% endcodeblock %}
這樣就完成我們的基本設定拉～ 開始寫code摟

我們開一個叫GSheetService的檔案，

如果有在laravel做分層架構的話，就把這檔案放到你的service層 (( 商業邏輯層、共用類別... 巴拉巴拉的地方

{% codeblock lang:php %}
<?php

namespace App\Services;

use Sheets;

class GSheetService
{
    /**
     * Sheet
     */
    public function Sheet($sheet_id, $sheet_name)
    {
        return Sheets::spreadsheet($sheet_id)->sheet($sheet_name); 
    }

    /**
     * Get sheet's data
     */
    public function Get($sheet_id, $sheet_name)
    {
        return $this->Sheet($sheet_id, $sheet_name)->get();
    }

    /**
     * Append data to sheet
     */
    public function Append($sheet_id, $sheet_name, $data = [])
    {
        return $this->Sheet($sheet_id, $sheet_name)->append([$data]);
    }

    /**
     * Update sheet's all data
     */
    public function Update($sheet_id, $sheet_name, $data = [])
    {
        return $this->Sheet($sheet_id, $sheet_name)->update([$data]);
    }
}
{% endcodeblock %}
在這個service中，我們將作者提供的功能稍微包裝成幾個package供我們的程式使用，

然後我們回到Controller中，將剛才的Service引用進去，
{% codeblock lang:php %}
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Services\GSheetService;

class SheetController extends Controller
{
    protected $gsheetservice;
    /**
     * 建構子
     */
    public function __construct(GSheetService $gsheetservice)
    {
        $this->gsheetservice = $gsheetservice;
    }

    public function Sheet()
    {
        $sheets = $this->gsheetservice->Get(config('google.post_spreadsheet_id'), config('google.post_sheet_id'));

        dd($sheets);
    }

    public function AddSheet()
    {
        $data = ['2020-4-19', '3000', '2000', '', 'test'];
        $sheets = $this->gsheetservice->Append(config('google.post_spreadsheet_id'), config('google.post_sheet_id'), $data);
        $sheets = $this->gsheetservice->Get(config('google.post_spreadsheet_id'), config('google.post_sheet_id'));
        dd($sheets);
    }
}
{% endcodeblock %}

之後就是到 web/router.php 設定一下簡單的路由
{% codeblock lang:php %}
Route::get('/sheet', 'SheetController@Sheet');
Route::get('/sheetadd', 'SheetController@AddSheet');
{% endcodeblock %}

我們先測試get吧，
![laravel2](../../../../image/gsheet/laravel2.png "laravel2")
看到這個畫面就表示成功取得google sheet的資料拉～

那換測試新增看看，
![laravel3](../../../../image/gsheet/laravel3.png "laravel3")
陣列長度變3，也可以看到剛剛新增的資料～～

完成拉

***
後記

其實google近幾年把api文件整理的比較清楚了，

但教學還是優先在oauth2登入的部分，

但我們不需要一個登入畫面給使用者登入，我們只需要一個權限去讀取檔案而已。

至於刪除～～ package似乎沒有這function，

粗步推估要get，刪除某一筆後，在update回sheet中～～

下次再做個py版的，也是有套件輔助，方便許多

***
Ref
[Google Sheets API v4 for Laravel](https://github.com/kawax/laravel-google-sheets)
[CONNECTING LARAVEL TO A GOOGLE SHEET](https://drivemarketing.ca/en/blog/connecting-laravel-to-a-google-sheet/)