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

> 在接下來的程式開發前，需要先行建置 Node.js 搭配 TypeScript 專案的初始化，會逐一解釋各步驟的理由及效果。
> * tags: `nodejs`, `typescript`

## 一、從 JavaScript 到 TypeScript

後端語言、框架的選擇很多，Node.js 是一種後端開發的框架，採用了 Google 所維護的 **V8** 引擎，是一款運行 JavaScript 的引擎，讓 JS 的領域從瀏覽器再延伸出去。

而 JavaScript 語言本身的 *Event-driven* 特性，搭配 Node.js 的 *non-blocking* 特性，讓 Node.js 相當適合高併發的應用場景。

但 Node.js 也因為 JavaScript 其它的語言特性，會受到一些先天性的限制。

### 1. 成也 JavaScript 敗也 JavaScript

Node.js 原封不動將 JavaScript 那自由奔放（變數作用域）、放蕩不羈（動態弱型別）的特性，毫不保留地轉移到了後端服務上。前者還可以透過團隊開發規範（code convention）迴避，但後者當專案規模隨著時間成長，很容易變成難以控制的局面。

基於這些原因，其實技術選型的情形下，除了自身的熟稔程度外，每個語言、框架所擅長的領域略有相同。

> ![](/textures/notice.png) 關於更多的「動態弱型別」 ![](/textures/notice.png)
>
> ---
>
> 推薦可以閱讀 2019 鐵人賽系列文，可以獲得更多切身之痛的案例：
> * **onejar99** ─ [你不可不知的 JavaScript 二三事](https://ithelp.ithome.com.tw/users/20112483/ironman/2016) Day 2-4

### 2. 救世主 TypeScript

TypeScript 的誕生原因是微軟在開發 JavaScript 專案時，缺乏了大型 web 專案該有的幾項要素，進而催生 TypeScript 的誕生。TypeScript 的主要作者是 C# 之父 *Anders Hejlsberg*，所以在撰寫會有些 C# 的影子存在。

以及經常會聽到的 **ES6**（含以上版本等），實際上是對應到特定版本號的 **ECMAScript**。**ECMAScript** 是訂定的語言標準，則 **JavaScript** 是實現 **ECMAScript** 並加上了 **DOM**、**BOM** 要素的語言，而 **TypeScript** 則是兩者的超集。
* **ECMAScript**
* **JavaScript**
  * 遵循 **ECMAScript** 的語言規範作為基礎
  * DOM (Document Object Model)：即全域物件 `document.`
  * BOM (Browser Object Model)：即全域物件 `windows.`
* **TypeScript**
  * 將 **JavaScript** 附加上強大 *Type System*

> ![](/textures/notice.png) 關於更多的 TypeScript 與 ECMAScript ![](/textures/notice.png)
>
> ---
>
> 推薦可以閱讀 2019 鐵人賽系列文，了解更完整的 TypeScript 開發方式：
> * **Maxwell Alexius** ─ [讓 TypeScript 成為你全端開發的 ACE！](https://ithelp.ithome.com.tw/users/20120614/ironman/2685)
>
> 若是想聽聽更多 ECMAScript 的講古，推薦可以閱讀 2019 鐵人賽系列文：
> * **kjj6198** ─ [深入現代前端開發](https://ithelp.ithome.com.tw/users/20103565/ironman/2527) Day 2

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

在 `package.json` 中有一屬性 `scripts` 可以撰寫自定義腳本。

### 1. 自定義腳本

如剛才我們使用的 `yarn ts-node src/index.ts` 就很適合作為一個 script。只需要在 `package.json` 加入 `scripts` 物件，當中的 `key` 為欲成為指令的 token，`value` 為實際會運行的指令。

```json
{
  // ... 其他屬性

  // 自定義腳本
  "scripts": {
    // 未來只需輸入 `yarn start` 就可以達到相同效果
    "start": "ts-node ./src/index.ts"
  }
}
```

### 2. 多種執行方式

前面的例子會產生一個疑惑，原本使用的指令是 `yarn ts-node src/index.ts`，但填入的指令卻只剩下 `ts-node src/index.ts`。原因是在 `scripts` 中的指令，都會以 `yarn run` 的程式來執行，因此以下四種寫法效果是等價的：
* `yarn run ts-node ./src/index.ts`
* `yarn ts-node ./src/index.ts`
* `yarn run start`
* `yarn start`
