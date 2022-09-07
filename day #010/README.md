# Day 10: GitHub Actions 部署 GitHub Pages

<p align="center">
    <img src="./cover.png" width="100" />
</p>

<p align="center">
    鋁鋼龍 <code>#884</code>
</p>

<p align="center">
    身體猶如打磨過的金屬，<br>雖然輕而堅硬，卻有著容易生鏽的弱點。
</p>

---

## 快速導覽

> 經過前面幾天的爬蟲練習後，接著可以嘗試將爬蟲爬下的資料，放到 GitHub Pages 以靜態檔案的方式做存放，再搭配 GitHub Actions 設定排程定時進行重新爬取與更新。
> * tags: `github-actions`, `github-pages`, `ci-cd`

## 一、GitHub Pages

在 GitHub 上提供了一個免費網頁代管服務 *GitHub Pages*，若分支名稱為 `gh-pages` 能夠將該分支的內容作為靜態檔案的伺服器。

以前幾天的爬蟲程式為例，最終皆會在 `artifacts` 的路徑下產生對應的 JSON 檔案，只需要 checkout 到 `gh-pages`，並將 `artifacts` 路徑的檔案放入後提交後上傳即可。

### 1. GitHub Pages 的潛規則

因為 GitHub Pages 內建採用 Jekyll 作為文章、部落格的生成引擎，但此刻我們只作為靜態網站伺服器使用，必須在根目錄下新增 `.nojekyll` 檔案，內容留空即可，目的是為了告訴 GitHub 當前的 Pages 並非基於 Jekyll 所建構。

```bash
# 新增一空內容的文件 '.nojekyll'
touch .nojekyll
```

## 二、GitHub Actions

在過去 GitHub 專案若要串接 CI/CD 的功能，必須導入如 *CircleCI*、*Travis CI* 等持續整合服務。2019 年 GitHub 發佈了 *GitHub Actions*，整體使用起來與 GitLab 自家發展的一條龍生態系 *GitLab CI/CD* 相當類似。不過當中最為之一亮的特色，開發者間能夠過 Marketplace 將 Actions 流通分享且複用，能夠節省非常多的時間。

### 1. Marketplace

如先前我們所使用的 npm packages 概念雷同，在 GitHub Marketplace 上有其他開發者分享的 Actions。

> ![](/day%20%23010/github-markerplace.png)
> [*GitHub Marketplace*](https://github.com/marketplace?type=actions) 上依照性質分類 Actions

### 2. 自動更新 GitHub Pages

> ![](/day%20%23010/github-action-deploy-to-github-pages.png)
> GitHub Action: [deploy-to-github-pages](https://github.com/marketplace/actions/deploy-to-github-pages)

人工部署作業勞心勞力，像是部署這類反覆性工作就相當適合使用 CI/CD 來達成自動化，同時還能夠減少人為的疏失。

新增檔案 `.github/workflows/publish-github-pages.yml`，並加入 `JamesIves/github-pages-deploy-action@v4` Action 來實現自動部署 GitHub Pages。

```yml
name: publish github pages

on:
  # 只有當 push commit 至 main 時才會觸發
  push:
    branches:
      - main
jobs:
  build:
    name: Publish GitHub Pages
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16.x]
    steps:
      # 在目前的 workspace 中，checkout 至當前 repo commit
      - uses: actions/checkout@v3

      # 接下來 3 個 steps 可依自行需求調整，這裡是 Node.js 專案的起手式
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      # 依賴項目安裝
      - name: Installation
        run: |
          yarn install

      # 運行主邏輯，這邊執行後會生成 'artifacts' 目錄的輸出檔案
      - name: Generate JSON files
        run: |
          yarn start

      - name: Commit files to gh-pages 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          # 預計將當前 workspace 指定路徑作為 GitHub Pages 的根目錄
          folder: artifacts
          # gh-pages 的分支名稱，默認為 'gh-pages'
          branch: gh-pages
```

### 3. 讓 workflow 永續運行

> ![](/day%20%23010/github-action-keepalive-workflow.png)
> GitHub Action: [keepalive-workflow](https://github.com/marketplace/actions/keepalive-workflow)

當項目內沒有提交行為長達 60 天時，為了避免不活躍的專案項目的自動排程不斷運行，GitHub 偵測使用 cron 觸發的 workflow 便會自動停擺，使用者必須主動開啟延長運行的請求。

> ![](/day%20%23010/gitlab-action-workflow-disable.png)
> *在不知情限制的狀況下，例行的排程被默默地暫停*

此時，需要新增檔案 `.github/workflows/keep-workflow-alive.yml`，並加入 `gautamkrishnar/keepalive-workflow@master` 這項社群 Action 來規避限制。

```yml
name: keep workflow alive
on:
  schedule:
    # 伺服器時間午夜 00:00 會自動觸發此 scheduled workflow
    - cron: '0 0 * * *'

jobs:
  keepalive-workflow:
    name: Keep workflow alive
    runs-on: ubuntu-latest
    steps:
      # 在目前的 workspace 中，checkout 至當前 repo commit
      - uses: actions/checkout@v3
      # 定期產生 empty commit
      - uses: gautamkrishnar/keepalive-workflow@master
```

根據 **keepalive-workflow** 的文件提到，當 repo 內的最後一次 commit 時間已逾 50 天以上，此 action 將會創建一個 empty commit 保持專案的活躍狀態，以達到無限期的 workflow 運行。

## 三、效果預覽

在 GitHub 的分支預覽，呈現效果如下：

![](/day%20%23010/gh-pages-preview-on-github.png)

並且圖片中的檔案能夠透過以下網址進行訪問：

``` bash
# https://<組織或用戶名稱>.github.io/<repo 名稱>/<檔案的相對路徑>
https://pmgo-professor-willow.github.io/data-bulbapedia/communityDays.json
```

在後續的主題，將會以這些 GitHub Pages 上的 JSON 檔案作為資料源。

## 四、範例原始碼

完整的範例原始碼紀錄於：https://github.com/pmgo-professor-willow/data-bulbapedia/tree/main/.github/workflows
