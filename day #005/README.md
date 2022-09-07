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

## 一、TBD

後端語言、框架的選擇很多，而 Node.js 因為 JavaScript 語言特性緣故。

Pros / Cons

> 成也 JavaScript 敗也 JavaScript

Node.js 原封不動將 JavaScript 那自由奔放、放蕩不羈的特性，毫不保留地轉移到了後端服務上。

...

> 救世主 TypeScript

## 二、建置專案

### 1. 專案初始化

第一步，先開啟一個空白專案後並在 terminal 輸入 `yarn init` 以生成 `package.json`。

```bash
# '-y' 即 '--yes'，將不會逐一詢問配置，直接使用默認設定，之後都機會能夠調整
yarn init -y
```

> ![](../textures/notice.png) package.json ![](../textures/notice.png)
>
> ---
>
> `package.json` 存在於 JavaScript / Node.js 的根目錄下，當中包含了項目的 metadata，用以管理依賴項目、腳本和版本等。
>
> * 完整的 properties 列表可參考 npm 官方文件：https://docs.npmjs.com/cli/v8/configuring-npm/package-json

### 2. 安裝 TypeScript

在此階段，執行 `tsc --init` 在根目錄下生成 `tsconfig.json`。

```bash
# '-D' 即 '--dev'，會將依賴項目安裝到 `devDependencies`
yarn add -D typescript

# TS 初始化
yarn tsc --init
```

> ![](../textures/notice.png) tsconfig.json ![](../textures/notice.png)
>
> ---
>
> `tsconfig.json` 的存在表明了此 TypeScript 項目的根目錄，當中記錄了編譯項目時所使用的選項。

### 3. 設定 git


### 4. 編寫一個 hello world


### 5. 建立測試


### 6. 設定腳本


## 三、結語


