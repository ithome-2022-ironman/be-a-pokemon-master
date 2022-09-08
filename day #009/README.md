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

這次爬蟲入門，我們選擇的案例和前幾天有些不同。嘗試從 BulbaPedia 的爬取寶可夢社群日資料，並將其結構化儲存。
* https://bulbapedia.bulbagarden.net/wiki/Community_Day

![](/day%20%23009/bulbapedia-community-day-events.png)

### 1. 為什麼下載的 HTML 和瀏覽時不同？

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

其實接下來運行的每一行，和使用者在操作的動作根本大相徑庭。

```ts
import puppeteer from 'puppeteer';

const getCommunityDays = async () => {
  const url = 'https://bulbapedia.bulbagarden.net/wiki/Community_Day';
  // headless 關閉後，會啟動本地有介面版本的 Chromium
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

還記得前面提到，我們要「盡可能模仿」一般使用者的瀏覽行為嗎？最直白的方法就是直接開一個瀏覽器啦！

```ts
const browser = await puppeteer.launch({ headless: false });
```

當 option 設定為 `headless: false` 後，執行程式會開啟 Chromium，而它看起來和 Chrome 其實差不多！預設為 `true`，便會在背景執行，當開發完成後可以考慮將 `headless` 開啟。

> ![](/day%20%23009/chromium.png)
> *自動執行了 Chromium，並開啟了指定的網頁*

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

`page.evaluate()` 由字面上可以解讀為「當前的 `page`」執行「function 所指定行為」。

將從 Node.js 的環境切換至 Chromium 內執行，在 Chromium 取得 `outerHTML` 並回傳回 Node.js。

> ![](/day%20%23009/pupperteer-evaluate-diagram.png)
> `page.evaluate()` 的執行流程，圖片取自於：https://stackoverflow.com/a/52046312*

## 三、querySelector 再度登場

將過去幾天所學習到的 **querySelector** 技巧套用進來，還是得強調，由於爬蟲爬的是別人的網站，在撰寫的過程中難免會大量出現 magic number 是無可厚非的。而解析 HTML 的過程經常需要見招拆招，建議還是多多練習，速度自然會起來。

### 1. 理解 DOM 結構，取得重複性的最小單位

從 DOM 結構看起來，我們需要取得每一張 table 的 `<tr>`，於是 selector 將會編寫為：`table.roundtable tbody tr`。

![](/day%20%23009/dom-tree.png)

```ts
// 解析原始 HTML 成樹狀結構
const root = parse(html);
const rowItems = root.querySelectorAll('table.roundtable tbody tr');
```

### 2. 排除 table header

迭代處理 `rowItems` 時，會發現每張 table 的第一個 `<tr>` 都是 header，並且當中不會有任何 `<td>`，只會有 `<th>`，我們便可以針對這點去做設計。

```ts
const communityDays: CommunityDay[] = [];

for (const rowItem of rowItems) {
  const tdItems = rowItem.querySelectorAll('td');
  const isHeader = !tdItems.length;

  if (isHeader) {
    continue;
  }

  // 還沒有結束哦！
}
```

### 3. 從每則 row 中萃取資訊

```ts
const communityDays: CommunityDay[] = [];

for (const rowItem of rowItems) {
  // 延續剛剛的程式碼

  // 主題寶可夢的名稱
  const featuredPokemon = tdItems[0].querySelector('a')?.getAttribute('title');
  // 社群日日期
  const date = tdItems[1].getAttribute('data-sort-value');
  // 社群日可以習得的限定招式
  const moves = tdItems[2].querySelectorAll('a')?.map(e => e.getAttribute('title')!).filter(s => s?.includes('(move)')).map(s => s?.replace(' (move)', ''));
  // 能夠獲得限定招式的寶可夢名稱
  const eligiblePokemons = tdItems[3].querySelectorAll('span a')?.map(e => e.getAttribute('title'));
  // 將資料 push 進 `communityDays` 陣列
  eligiblePokemons.forEach(eligiblePokemon => communityDays.push({ featuredPokemon, eligiblePokemon, moves, date }));

  return communityDays.filter(c => c.featuredPokemon);
}
```

### 4. 發生例外的狀況，rowspan

多數的社群日都是主題寶可夢進化為特定一種寶可夢，但是這個例外發生在伊布身上了，在 BulbaPedia 的處理方式是透過 rowspan 讓第二個開始的 `<tr>` 只剩下兩個 `<td>`。

![](/day%20%23009/dom-tree-2.png)

```ts
const communityDays: CommunityDay[] = [];

for (const rowItem of rowItems) {
  // 延續剛剛的程式碼

  if (tdItems.length === 6) {
    // 延續剛剛的程式碼
  } else if (tdItems.length === 2) {
    // 社群日可以習得的限定招式
    const moves = tdItems[0].querySelectorAll('a')?.map(e => e.getAttribute('title')!).filter(s => s?.includes('(move)')).map(s => s?.replace(' (move)', ''));
    // 能夠獲得限定招式的寶可夢名稱
    const eligiblePokemon = tdItems[1].querySelector('a')?.getAttribute('title');
    // 將缺少的資訊以前一筆繼承，將資料 push 進 `communityDays` 陣列
    const lastCommunityDay = _.last(communityDays);
    communityDays.push({ ...lastCommunityDay, eligiblePokemon, moves });
  }

  return communityDays.filter(c => c.featuredPokemon);
}
```

## 四、範例原始碼

### 1. 此次範例原始碼整理

```ts
// Node modules.
import _ from 'lodash';
import puppeteer from 'puppeteer';
import { parse } from 'node-html-parser';
// Local modules.
import { hostUrl } from './utils';

interface CommunityDay {
  featuredPokemon?: string;
  eligiblePokemon?: string;
  moves?: string[];
  date?: string;
}

const getCommunityDays = async () => {
  const url = 'https://bulbapedia.bulbagarden.net/wiki/Community_Day';
  const browser = await puppeteer.launch({ headless: false });
  const page = await browser.newPage();
  await page.goto(url, { waitUntil: 'networkidle0' });
  const html = await page.evaluate(() => document.querySelector('*')?.outerHTML!);
  await browser.close();

  const root = parse(html);
  const rowItems = root.querySelectorAll('table.roundtable tbody tr');

  const communityDays: CommunityDay[] = [];
  
  for (const rowItem of rowItems) {
    const tdItems = rowItem.querySelectorAll('td');
    const isHeader = !tdItems.length;

    if (isHeader) {
      continue;
    }

    // First row.
    if (tdItems.length === 6) {
      const featuredPokemon = tdItems[0].querySelector('a')?.getAttribute('title');
      const date = tdItems[1].getAttribute('data-sort-value');
      const moves = tdItems[2].querySelectorAll('a')?.map(e => e.getAttribute('title')!).filter(s => s?.includes('(move)')).map(s => s?.replace(' (move)', ''));
      const eligiblePokemons = tdItems[3].querySelectorAll('span a')?.map(e => e.getAttribute('title'));

      eligiblePokemons.forEach(eligiblePokemon => communityDays.push({ featuredPokemon, eligiblePokemon, moves, date }));
    }
    // Second row, third row ...
    else if (tdItems.length === 2) {
      const moves = tdItems[0].querySelectorAll('a')?.map(e => e.getAttribute('title')!).filter(s => s?.includes('(move)')).map(s => s?.replace(' (move)', ''));
      const eligiblePokemon = tdItems[1].querySelector('a')?.getAttribute('title');
      const lastCommunityDay = _.last(communityDays);

      communityDays.push({ ...lastCommunityDay, eligiblePokemon, moves });
    }
  }

  return communityDays.filter(c => c.featuredPokemon);
};

const main = async () => {
  const communityDays = await getCommunityDays();
};

main();
```

### 2. 更完整的範例

更完整的範例原始碼紀錄於：https://github.com/pmgo-professor-willow/data-bulbapedia

> ![](/day%20%23009/preview.png)
> *完整範例的參考[輸出結果](https://github.com/pmgo-professor-willow/data-bulbapedia/blob/gh-pages/communityDays.json)*
