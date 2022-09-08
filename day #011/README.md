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
* 發布時間
* 影片縮圖

![](/day%20%23011/youtube-channel.png)

接著試試這幾天前所學的方法能否成功沿用，一樣先行開啟 devtool 並找到對應的影片卡片。

嘗試觀察 DOM 結構後卻發現「發布時間」提供的竟是相對時間而非絕對時間，如此一來資料就會不夠精準，必須另尋他法。

![](/day%20%23011/dom-tree.png)

## 二、使用 YouTube Data API

### 1. 閱讀官方文件

因此可以考慮透過 *YouTube Data API* 來實現這方面的資訊，先至官方文件找取得的播放清單的影片列表 API：

> ![](/day%20%23011/youtube-data-api-playlistitems-list.png)
> *PlaylistItems: list 的回傳結果*

接著得知 **Video Resource** 的資料格式：

> ![](/day%20%23011/youtube-data-api-playlistitems-resource.png)
> *PlaylistItem Resource 的資料格式*

便可確立欲收集的四種資訊皆可透過 API 獲得，其中發布時間的確提供了精準時間的 ***datetime***。

* 影片標題：`snippet.title` (***string***)
* 影片連結：`snippet.channelId` (***string***) ─（可由 ID 加工組合成影片連結網址）
* 發布時間：`snippet.publishedAt` (***datetime***)
* 影片縮圖：`snippet.thumbnails.*.url` (***string***)

### 2. 取得 auth token

接下來的過程，我們會需要申請一把 API 金鑰。若只是做練習或是小專案，API 呼叫的免費額度其實相當足夠的。若是擔心會用盡，可以參考官方的 [Quota Calculator](https://developers.google.com/youtube/v3/determine_quota_cost)。

1. 至 [Google Cloud Platform](https://console.cloud.google.com/) 申請 project
2. 在 Library 中搜尋 `YouTube Data API v3` 並 enable
  > ![](/day%20%23011/google-api-library.png)
3. 在 Credentials 的 API Keys 找到 `SHOW KEY`
  > ![](/day%20%23011/google-api-credentials.png)

### 3. 使用官方 SDK

首先安裝官方的 `googleapis` 函式庫。

```bash
yarn add googleapis
```

根據前一節的官方文件，我們可以撰寫出最基本的 `getVideos()`：

```ts
import { google } from 'googleapis';

const getVideos = async (playlistId: string) => {
  const service = google.youtube('v3');
  const response = await service.playlistItems.list({
    auth: '需替換成自己的 token',
    playlistId: '需替換播放清單 ID',
  });
  return response.data.items;
};
```

### 4. 取得播放清單 ID

```ts
const getChannelByUsername = async (username: string) => {
  const service = google.youtube('v3');
  const response = await service.channels.list({
    auth: '需替換成自己的 token',
    part: ['snippet', 'statistics'],
    forUsername: username,
  });

  // 此時的 channel.id 就是該頻道的 ID
  const [channel] = response.data.items || [];

  return channel;
};
```

> ![](/textures/notice.png) 用頻道編號得知播放清單編號 ![](/textures/notice.png)
>
> ---
>
> 這裡有一個潛規則，`channelId` 字首 'UC' 的 C 表示 Channel，若改為 'UU' 即為 'Upload'：
> * `UCoOY8_LHn6EBM25BN2_z0Ww` => `UUoOY8_LHn6EBM25BN2_z0Ww`
> * 參考資料：https://stackoverflow.com/a/27872244

### 5. 根據需求進行微調

```ts
import { google } from 'googleapis';

const getVideos = async (channelId: string) => {
  // 使用 Google API 中的 YouTube Data API
  const service = google.youtube('v3');
  const response = await service.playlistItems.list({
    auth: '需替換成自己的 token',
    part: ['snippet'],
    playlistId: channelId.replace('UC', 'UU'),
    maxResults: 10,
  });

  // 取得 videos 陣列，並進行加工
  const videos = response.data.items || [];
  const results = videos.map((video) => ({
    // 影片標題
    title: String(video.snippet?.title),
    // 連結藉由影片 ID 組合而成
    url: `https://www.youtube.com/watch?v=${video.snippet?.resourceId?.videoId}`,
    // 發布時間
    publishedAt: String(video.snippet?.publishedAt),
    // 縮圖有多種解析度，但都不是必定提供，所以這裡列舉了數種
    thumbnailUrl: String(
      video.snippet?.thumbnails?.standard?.url ||
      video.snippet?.thumbnails?.high?.url ||
      video.snippet?.thumbnails?.default?.url
    ),
  }));

  return results;
};
```

## 三、結語

TBD
