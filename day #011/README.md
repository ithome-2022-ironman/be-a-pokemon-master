# Day 11: 串接 YouTube API 取得影片資料

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    洛托姆（加熱洛托姆） <code>#479</code>
</p>

<p align="center">
    會烤焦你最愛的衣服。<br>喜歡用加熱來惡作劇的洛托姆。
</p>

---

## 快速導覽

> TBD
> * tags: `nodejs`, `youtube`, `youtube-data-api`

## 一、以爬蟲技巧嘗試取得

首先我們先來看看這次預計爬取的資料原貌，是 YouTube 的頻道影片列表，預計從每部影片至少取得幾種資訊：
* 影片標題
* 影片連結
* 發布日期
* 影片縮圖

![](/day%20%23011/youtube-channel.png)

接著試試這幾天前所學的方法能否成功沿用，一樣先行開啟 devtool 並找到對應的影片卡片。

嘗試觀察 DOM 結構後卻發現「發布日期」提供的竟是相對時間而非絕對時間，如此一來資料就會不夠精準，必須另尋他法。

![](/day%20%23011/dom-tree.png)

## 二、使用 YouTube Data API

### 1. 閱讀官方文件

因此可以考慮透過 *YouTube Data API* 來實現這方面的資訊，先至官方文件找取得的影片列表的 API：

> ![](/day%20%23011/youtube-data-api-video-list.png)
> *Videos: list 的回傳結果*

接著得知 **Video Resource** 的資料格式：

> ![](/day%20%23011/youtube-data-api-video-resource.png)
> *Video Resource 的資料格式*

便可確立欲收集的四種資訊皆可透過 API 獲得，其中發布日期的確提供了精準日期的 ***datetime***。

* 影片標題：`snippet.title` (***string***)
* 影片連結：`snippet.channelId` (***string***) --- （可由 ID 加工組合成影片連結網址）
* 發布日期：`snippet.publishedAt` (***datetime***)
* 影片縮圖：`snippet.thumbnails.*.url` (***string***)

### 2. 使用官方 SDK

TBD

### 3. 撰寫程式

TBD

## 三、結語

TBD
