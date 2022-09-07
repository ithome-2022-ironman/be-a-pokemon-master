# Day 05: Node.js + TypeScript 專案相關建置

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

在接下來的程式開發前，需要先行建置 Node.js 搭配 TypeScript 專案的初始化，會逐一解釋各步驟的理由及效果。

* tags: `nodejs`, `typescript`

## 一、TBD

後端語言、框架的選擇很多，而 Node.js 因為 JavaScript 語言特性緣故。

Pros / Cons

> 成也 JavaScript 敗也 JavaScript

Node.js 原封不動將 JavaScript 那自由奔放、放蕩不羈的特性，毫不保留地轉移到了後端服務上。

TBD

> 救世主 TypeScript

TBD

## 二、建置專案

### 1. 專案初始化

開啟一個空白專案後並在 terminal 輸入 `yarn init` 以生成 `package.json`。

```bash
# '-y' 即 '--yes'，將不會逐一詢問配置，直接使用默認設定，之後都機會能夠調整
yarn init -y
```

> ![](/textures/notice.png) package.json ![](/textures/notice.png)
>
> ---
>
> `package.json` 存在於 JavaScript / Node.js 的根目錄下，當中包含了項目的 metadata，用以管理依賴項目、腳本和版本等。
>
> * 完整的 properties 列表可參考 npm 官方文件：https://docs.npmjs.com/cli/v8/configuring-npm/package-json

### 2. 安裝依賴項目

在接下來幾天的練習，我們會使用到 TypeScript 進行撰寫，因此會需要 `typescript` 套件進行 `tsc` 的編譯，將 `.ts` 編譯為 `.js`。

再來是作為輔助開發用途的 `ts-node` 套件，因為 Node.js 僅接受執行 `.js` 的檔案，所有的 `.ts` 都必須先透過 `tsc` 編譯成 `.js`。而 `ts-node` 提供了一個管道，無須事先編譯卻能夠直接執行 TS 檔案。

```bash
# '-D' 即 '--dev'，會將依賴項目安裝到 `devDependencies`
yarn add -D typescript ts-node
```

> ![](/textures/notice.png) `yarn.lock` 與 `node_modules` ![](/textures/notice.png)
>
> ---
>
> 當首次安裝依賴項目後，根目錄下自動生成了 `yarn.lock` 檔案與  `node_modules` 路徑。
> * `yarn.lock`：目的是將依賴項目與其巢狀關係的依賴項目全部攤平，將個依賴項目的版本資訊記錄下來，讓不同時間點重新下載時都能夠保證依賴項目的版本一致。
> * `node_modules`：依賴項目的函式庫被攤平的安裝至 `node_modules` 中。

### 3. TypeScript 的初始化

執行 `tsc --init` 在根目錄下生成 `tsconfig.json`，當中記錄了編譯項目時所使用的選項。

```bash
# TS 初始化
yarn tsc --init
```

### 4. 編寫一個 hello world

新建檔案 `src/index.ts`。

```ts
function main() {
  console.log('Hello World');
}

main();
```

在生產環境時，會先將 `.ts` 編譯成 `.js` 再來執行。不過目前仍在開發初期，透過 `ts-node` 直接執行 TS 檔案較為方便、快速。

```bash
# 執行 index.ts
yarn ts-node src/index.ts
```

### 5. 加入 .gitignore

在專案項目內，會有一些較為敏感的資訊（如密碼或金鑰），或是編譯過程中的中間產物（如 `./node_modules`）。而版本控制中應盡量避免存放這些檔案，便可以透過 `.gitignore` 做到。

```bash
# 將 .gitignore 放到根目錄下，再依據需求去編輯
touch .gitignore
```

筆者習慣使用 GitHub 所維護的 `.gitignore`，再下去做微調：
* github/gitignore: https://github.com/github/gitignore/blob/main/Node.gitignore

> ![](/textures/notice.png) 注意事項 ![](/textures/notice.png)
>
> ---
>
> * `package.json` 與 `yarn.lock` 會紀錄依賴項目的版本資訊，務必兩者都需要加入至版本控制中，務必記得不可以寫入至 `.gitignore` 之中
> * `node_modules` 是預設的忽略路徑。若重新 clone 專案時，務必記得先執行 `yarn install` 將依賴項目全部下載安裝

## 三、腳本設置

在 `package.json` 中有一屬性 `scripts` 可以撰寫自定義腳本來輔助

TBD
