# Day 09: 爬蟲設計入門班 ④: 使用 Puppeteer 進行爬蟲作業

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    謎擬Ｑ <code>#778</code>
</p>

<p align="center">
    為了讓別人不要害怕自己，特意穿上了看似皮卡丘的破布，<br>結果卻變得更加令人毛骨悚然。
</p>

---

## 快速導覽

> * tags: `nodejs`, `typescript`, `puppeteer`, `web-crawler`, `web-scraping`

## 一、BulbaPedia

這次爬蟲入門，我們選擇的案例和前幾天有些不同。嘗試從 BulbaPedia 的爬取資料社群日資料，並將其結構化儲存。
* https://bulbapedia.bulbagarden.net/wiki/Community_Day

![](/day%20%23009/bulbapedia-community-day-events.png)

### 1. TBD

### 2. 為什麼下載的 HTML 和瀏覽時不同？

使用工具 `curl` 直接訪問指定網址後，理應上應該要取得瀏覽器訪問時相同的 HTML，但事與願違。

```bash
curl https://bulbapedia.bulbagarden.net/wiki/Community_Day
```

> ![](/day%20%23009/curl.png)
> *curl 所回傳的 HTML 當中有段訊息「Please enable JS and disable any ad blocker」*

看來網站的渲染方式不同，有別於先前的例子會直接取得完整的 HTML，必須實際執行 JS 才有機會取得完整的渲染結果，這時候我們必須盡可能模仿出一般使用者的瀏覽行為。

## 二、Puppeteer

*Puppeteer* 提供 API 來控制本地的 Chromium，藉以模擬出一般用戶使用瀏覽器的行為，以達到爬取需要執行 JavaScript 腳本才會渲染內容的網站（如 AJAX）或是撰寫端對端測試（E2E testing）。

### 1. 取得 HTML

```ts
import puppeteer from 'puppeteer';

const getCommunityDays = async () => {
  const url = 'https://bulbapedia.bulbagarden.net/wiki/Community_Day';
  // headless 關閉後，會啟動本地的 Chromium
  const browser = await puppeteer.launch({ headless: false });
  // 開啟新分頁
  const page = await browser.newPage();
  // 當前分頁前往指定網址，並且使用 networkidle0 作為停止的條件（後面補充說明）
  await page.goto(url, { waitUntil: 'networkidle0' });
  // 當前分頁執行指定的 script，這邊是回傳 HTML
  const html = await page.evaluate(() => document.querySelector('*')?.outerHTML!);
  // 關閉瀏覽器
  await browser.close();

  // 還沒有結束哦！
};
```

#### 1-1. BrowserLaunchArgumentOptions: headless

還記得前面提到，我們要「盡可能模仿」一般使用者的瀏覽行為嗎？

```ts
const browser = await puppeteer.launch({ headless: false });
```

當 option 設定為 `headless: false` 後，執行程式會開啟 Chromium，而它看起來和 Chrome 其實差不多！

> ![](/day%20%23009/chromium.png)
> *Chromium 開啟了指定的網頁*

#### 1-2. WaitForOptions: waitUntil

```ts
await page.goto(url, { waitUntil: 'networkidle0' });
```

透過以上的程式，現階段可以取得完整的 HTML，當中最有趣的是 `waitUntil` 的機制：
* `networkidle0`: 在 500ms 內沒有任何網路請求，即視為導航結束（適用於 SPA 的情境）
* `networkidle2`: 在 500ms 內不超過兩個網路請求，即視為導航結束（適用於有 Long-polling 的情境）
* `load`: 當 `window.onload` 事件被觸發時導航結束
* `domcontentloaded`: 當 document 被完整的讀取跟解析後導航結束

以上狀態可以併行使用，改成字串陣列即可（如：`['networkidle0', 'domcontentloaded']`）。

#### 1-3. page.evaluate()

```ts
const html = await page.evaluate(() => document.querySelector('*')?.outerHTML!);
```

### 2. TBD

