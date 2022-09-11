# Day 07: 爬蟲設計入門班 ②: LeekDuck 遊戲資料站

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    大蔥鴨（伽勒爾的樣子） <code>#083</code>
</p>

<p align="center">
    在不斷活用又粗又長的伽勒爾大蔥的過程中，<br>大蔥鴨演化成了獨特的樣子。
</p>

---

## 快速導覽

> 從昨天實作中完整順過一個簡易爬蟲開發，今天會將這些步驟歸納出爬蟲的基本流程，並練習不同的案例！
> * tags: `nodejs`, `typescript`, `web-crawler`, `web-scraping`

## 一、網路爬蟲的開發流程

根據昨天 Pokemon GO 官方網站的爬蟲開發案例，爬蟲程式的開發可以總結以下步驟：
1. 決定欲搜集資料的網頁
2. 檢查網頁的代碼（DOM 結構）
3. 找到要萃取的數據
4. 準備開發環境並撰寫程式碼
5. 運行程式並驗證結果是否如預期
6. 將取得的數據儲存下來

過程中會從步驟 3 至步驟 5 反覆驗證、開發，最終得到期望的數據資料。

## 二、LeekDuck 遊戲資料站

我們預計從 LeekDuck 遊戲資料站抓取 Pokemon GO 的團體戰資訊。
* https://www.leekduck.com/boss

> ![](/textures/notice.png) Pokemon GO 特色玩法：團體戰（Raid） ![](/textures/notice.png)
>
> ---
>
> 當頭目寶可夢占領道館時，便會發生 **團體戰**。 你的目標是打敗這個強大的頭目寶可夢。 當你和其他訓練家同心協力擊敗頭目寶可夢後，便可獲得只有贏得團體戰時才能得到的特殊道具，並有機會捕捉被擊敗的頭目寶可夢。 團體戰需要互相合作。和其他訓練家同心協力，一起挑戰強大的頭目寶可夢吧。 ─ 引自於 *Pokemon GO 官方幫助中心*

### 1. 先將資料分群

透過 Google Chrome 的 devtool 檢查 DOM 的結構，先行聚焦在 `#raid-list` 節點上：

![](/day%20%23007/leekduck-list.png)

可以觀察到團體戰難度（Tier）是 classname `header-li` 的節點，首領寶可夢資訊紀錄在 classname `boss-item` 的節點，且攤平於同一層。因此在後續解析時，必須以相同團體戰難度為同群來處理。

### 2. 解析最小的處理單位

接著，頭目寶可夢的資料節點 `boss-item`，便會是此次處理的最小單位節點：

![](/day%20%23007/leekduck-pokemon-item.png)

根據節點結構大致可以用以下 selectors 劃分成：
* `.boss-img`
  * `img`: 寶可夢圖片
  * `img.shiny-icon`: 是否開放捕捉異色，若無則此節點不存在
* `.boss-1`: 
  * `p.boss-name`: 寶可夢名稱
  * `.boss-type`
    * `img.type1`: 寶可夢第一屬性
    * `img.type2`: 寶可夢第二屬性，若無則此節點不存在
* `.boss-2`: 正常 CP 值上限與下限
* `.boss-3`
  * `.boss-weather`
    * `img.weather1`: 根據寶可夢第一屬性，天氣加成的條件
    * `img.weather2`: 根據寶可夢第二屬性，天氣加成的條件，若無則此節點不存在
  * `.boosted-cp`: 天氣加成的 CP 值上限與下限

## 三、撰寫爬蟲

話不多說，我們直接開始！

### 1. 取得 HTML 與所有頭目寶可夢資料

建立 `src/index.ts`：

```ts
import fetch from 'node-fetch';
import { parse } from 'node-html-parser';

const main = async () => {
  const url = 'https://leekduck.com/boss/';
  const res = await fetch(url);
  const html = await res.text();

  // 解析 HTML
  const root = parse(html);
  // 查詢 ID 為 'raid-list' 節點底下的 'ul.list li'
  const listItems = root.querySelectorAll('#raid-list ul.list li');
  const tierList: { tier: string, index: number }[] = [];
  const bossItems: HTMLElement[] = [];

  // 會有「團體戰難度的標題節點」，以及「頭目寶可夢資訊節點」
  listItems.forEach((listItem, i) => {
    const isHeader = listItem.getAttribute('class') === 'header-li';

    if (isHeader) {
      // 透過 selector 取的 header 節點，將其文字 'Tier 3' 正規化為 'tier 3'
      const tierText = listItem.querySelector('h2.boss-tier-header').lastChild.rawText;
      const tier = tierText.toLowerCase().replace('tier', '').trim();
      // 將目前的難度 index 一併記錄下來，方便後續分群時判斷
      const index = i - tierList.length;
      tierList.push({ tier, index });
    } else {
      // 將頭目寶可夢資訊記錄下來
      bossItems.push(listItem);
    }
  });

  // 還沒有結束哦！
};

main();
```

### 2. 將頭目寶可夢資料結構化

接著透過 `.querySelector()` 與 `.querySelectorAll()` 根據資料性質，可能為單筆或多筆，依實際狀況去調整迭代去處理。

```ts
// 文字的轉換小工具，原始文字為 '1548 - 1633'，將會轉換為 { min: 1548, max: 1633 }
const cpFormatter = (cpRawText: string) => {
  const matches = cpRawText.trim().match(/^(\d+)[^\d]+(\d+)$/);
  return matches
    ? { min: parseInt(matches[1]), max: parseInt(matches[2]) }
    : null;
};

const raidBosses = bossItems.map((bossItem, i) => ({
  // 剛才的 tierList 目的就是能夠知道指定頭目寶可夢的挑戰難度為何
  tier: _.maxBy(tierList.filter((o) => i >= o.index), 'index')?.tier,
  // 取得節點的文字部分作為寶可夢名稱
  name: bossItem.querySelector('p.boss-name').firstChild.rawText,
  // 由於圖片網址只會提供相對路徑，所以需要將 host URL 加入
  imageUrl: new URL(bossItem.querySelector('div.boss-img img').getAttribute('src')!, url).href,
  // 根據是否有找到節點決定是「否有開放捕捉異色」
  shinyAvailable: !!bossItem.querySelector('div.boss-img img.shiny-icon'),
  // 電系 'Electric' 會正規化為 'electric'；超能力系 'Psychic' 會正規化為 'psychic' ... 諸此類推
  types: bossItem.querySelectorAll('div.boss-type img').map((node) =>
    node.getAttribute('title')?.toLowerCase()
  ),
  // 由於圖片網址只會提供相對路徑，所以需要將 host URL 加入
  typeUrls: bossItem.querySelectorAll('div.boss-type img').map((node) =>
    new URL(node.getAttribute('src')!, url)
  ),
  // 這裡的 `lastChild` 是比較特別的做法，因為在 `div.boss-2` 中有兩個 children，後者是不帶有任何 tag 的節點
  cp: cpFormatter(bossItem.querySelector('div.boss-2').lastChild.rawText),
  boostedCp: cpFormatter(bossItem.querySelector('div.boss-3 span.boosted-cp').lastChild.rawText),
  // 雨天 'Rainy' 會正規化為 'rainy'；強風天 'Windy' 會正規化為 'windy' ... 諸此類推
  boostedWeathers: bossItem.querySelectorAll('div.boss-3 .boss-weather img').map((node) =>
    node.getAttribute('title')?.toLowerCase()
  ),
  // 由於圖片網址只會提供相對路徑，所以需要將 host URL 加入
  boostedWeatherUrls: bossItem.querySelectorAll('div.boss-3 .boss-weather img').map((node) => 
    new URL(node.getAttribute('src')!, url)
  ),
}));
```

## 四、範例原始碼

更完整的範例原始碼紀錄於：https://github.com/pmgo-professor-willow/data-leekduck

> ![](/day%20%23007/preview.png)
> *完整範例的參考[輸出結果](https://github.com/pmgo-professor-willow/data-leekduck/blob/gh-pages/raid-bosses.json)*
