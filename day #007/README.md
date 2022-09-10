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

> 從昨天實作中完整順過一個簡易爬蟲開發，今天會將這些步驟歸納出爬蟲的基本流程，屆時再練習不同的案例！
> * tags: `nodejs`, `typescript`, `web-crawler`, `web-scraping`

## 一、網路爬蟲的開發流程

根據昨天 Pokemon GO 官方網站的爬蟲開發案例，爬蟲程式的開發可以總結以下步驟：
1. 決定欲搜集資料的網頁
2. 檢查網頁的代碼（DOM 結構）
3. 找到要萃取的數據
4. 準備開發環境並開始撰寫程式碼
5. 運行程式並驗證結果是否如預期
6. 將取得的數據儲存下來

過程中會從步驟 3 至步驟 5 反覆驗證、開發，最終得到期望的數據資料。

## 二、LeekDuck 遊戲資料站

透過 Google Chrome 的 devtool 檢查 DOM 的結構，可以觀察到團體戰星數（Tier）是 classname `header-li` 的節點，首領寶可夢資訊紀錄在 classname `boss-item` 的節點，且攤平於同一層。

![](/day%20%23007/leekduck-list.png)

首領寶可夢的資料節點展開後，可以劃分成以下結構：
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

![](/day%20%23007/leekduck-pokemon-item.png)
