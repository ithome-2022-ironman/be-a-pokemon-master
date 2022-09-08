# Day 09: 爬蟲設計入門班 ④: 使用 Puppeteer 進行爬蟲作業

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    幼基拉斯 <code>#246</code>
</p>

<p align="center">
    靠吃泥土維生。<br>吃掉一座大山後，就會為了成長而開始沉睡。
</p>

---

## 快速導覽

> * tags: `nodejs`, `typescript`, `web-crawler`, `web-scraping`

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

看來網站有針對爬蟲機器人有一定程度的阻撓，這時候我們必須盡可能模仿出一般使用者的瀏覽行為。

## 二、Puppeteer

TBD
