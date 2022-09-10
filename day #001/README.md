# Day 01: 2022 年 09 月 ─ 放送開始

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    小火龍 <code>#004</code>
</p>

<p align="center">
    天生喜歡熱熱的東西。<br>據說當牠被雨淋濕的時候，尾巴的末端會冒出煙來。
</p>

---

## 一、冒險的起點

筆者是一名熱衷 Pokemon GO 的寶可夢訓練家，上班認真寫扣、下班努力抓寶。雖日復一日卻天天保有新鮮感，期待著每天的到來。

遊戲開發公司 **Niantic** 為了保留玩家對於遊玩、探索過程的新鮮感，在遊戲內的快訊經常無法得知活動的全貌。而限時活動的時效性，恐導致玩家無法有效地進行攻略而留下了遺憾。因此促進了各國熱心玩家整理的情報，當中可能是情報網站、文字訊息、圖文資訊或影片導覽 ... 等。

遊戲情報的資訊取得管道相當多元，來自於 *Twitter*、*YouTube*、*Facebook*、*巴哈姆特討論區* 與 *LINE 社群* 等各式各樣的平台。但在這幾年的遊戲過程中，筆者感受到最大的痛點就是「常態性複數活動併行，且各自的起訖時間不同」，時常會突然找不到某個曾看過的活動情報。

基於上述理由，萌生了此專案的開發念頭，希冀能夠將多方情報管道彙整為一，提供一個「不敢說是最全面，但絕對是最方便」的遊戲情報提供管道。

## 二、Pokemon GO 維羅博士

LINE 聊天機器人「Pokemon GO 維羅博士」自 2021 年 2 月上線。筆者抱著能夠獲得百名訓練家的共鳴就心滿意足，萬萬沒想到上線第一個月內達到 4,000 名好友，至今已逾萬名好友加入。過程中收到許許多多來自各方訓練家的建議與反饋，目前仍在持續更新及優化當中。

![](/day%20%23001/banner.png)

「Pokemon GO 維羅博士」收集國內外熱心玩家所彙整的情報，透過 web crawler 與社群平台 APIs 取得原始資料並將其結構化儲存，再利用 LINE ChatBot 將這些即時資訊提供給每位訓練家。

> ![](/day%20%23001/features.png)
> *目前聊天機器人有提供的功能一覽*

> 筆者認為工程師的核心價值就是「懶」，而上述提及的功能除非來源資料結構改版，基本上是全自動化的即時更新。

## 三、內容大綱

在這次的鐵人賽的第一階段中，筆者會從最初的專案雛形的設計開始分享，如何透過 Figma 來製作出可複用的元件，並產出能夠互動的 prototype。

第二階段，以 Node.js 搭配 TypeScript 開發系列 web crawler 和串接 YouTube API 和 Twitter API 進行資料的收集，並將結構化的資料以 GitHub Pages 的方式存取，並會分享 npm package 的發布方式。

第三階段，介紹以 golang 開發 LINE ChatBot 的過程，並教學如何使用 Google Cloud Functions，佐以 GitHub Actions 達到自動化部署。

第四階段，最後會使用 React 搭配 TypeScript 設計一個寶可夢圖鑑的網頁。

---

![以 Figma 進行專案項目的原型設計 (prototype)](/day%20%23001/ep01.png)

* Day 02: 紙上談兵
* Day 03: Figma 入門班 ①: 製作首個元件
* Day 04: Figma 入門班 ②: 製作原型設計

---

![以 Node.js + TypeScript 開發網路爬蟲 (web crawler) 收集網路資料](/day%20%23001/ep02.png)

* Day 05: Node.js + TypeScript 專案相關建置
* Day 06: 爬蟲設計入門班 ①: Pokemon GO 官方網站
* Day 07: 爬蟲設計入門班 ②: LeekDuck 遊戲資料站
* Day 08: 爬蟲設計入門班 ③: The Silph Road 遊戲資料站
* Day 09: 爬蟲設計入門班 ④: 使用 Puppeteer 進行爬蟲作業
* Day 10: GitHub Actions 部署 GitHub Pages
* Day 11: 串接 YouTube API 取得影片資料
* Day 12: 串接 Twitter API 取得推文資料
* Day 13: 製作 JS package，並發布到 npm

---

![以 golang 進行 LINE ChatBot 開發](/day%20%23001/ep03.png)

* Day 14: LINE Message API 介紹與首次設定
* Day 15: golang 專案建置與 LINE Messaging API SDK
* Day 16: LINE Messaging API 入門班 ①: 搭配 ngrok 開發，發送首個 Message
* Day 17: LINE Messaging API 入門班 ②: 發送 Flex Message
* Day 18: LINE Messaging API 入門班 ③: 以圖文選單進行互動
* Day 19: 並配合 VSCode debugging 設定斷點進行除錯
* Day 20: 部署至 Google Cloud Functions 並結合自動化部署

---

![以 React + TypeScript 進行 LINE LIFF 的網頁開發](/day%20%23001/ep04.png)

* Day 21: LIFF (LINE Front-end Framework) 介紹與設定
* Day 22: React + TypeScript 專案相關建置
* Day 23: React 入門班 ①: React 的生命週期
* Day 24: React 入門班 ②: React Component
* Day 25: React 入門班 ③: React Router
* Day 26: React 入門班 ④: React Hooks
* Day 27: React 入門班 ⑤: Styled Components

---

* Day 28: 預留做為 buffer
* Day 29: 預留做為 buffer
* Day 30: 賽末心得
