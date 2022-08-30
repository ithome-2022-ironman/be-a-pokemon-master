# 目標成為寶可夢大師

作者分享一個興趣使然的 side project 創作歷程，原先期望只會有數百名左右的用戶，卻意外獲得遊戲社群迴響。

LINE 聊天機器人「Pokemon GO 維羅博士」，從 2021 年初上線至今累計超過一萬名好友，改善許多台灣的寶可夢訓練家的遊戲體驗。

過程中會介紹 Figma 進行 prototyping，透過 NodeJS & TypeScript 進行爬蟲資料收集，及使用 golang 開發 LINE ChatBot 與 React 開發前端的 LINE LIFF，最後藉由 GitHub Actions 達成自動化部署。

## Outline

* Introduction (1 day)
* Prototyping (3 days)
  * Figma
    * components
* data sources
  * web crawler: NodeJS + TypeScript (4 days)
    * Easy: `data-pokemongolive`
    * Medium: `data-leekduck`, `data-thesilphroad`
  * CI/CD (1 days)
    * GitHub Actions
      * Auto deployment on GitHub Pages
      * keepalive-workflow
  * web crawler: using puppeteer (1 day)
    * `data-bulbapedia`
  * YouTube API (1 day)
    * `data-youtuber`
  * Twitter API (1 day)
    * `data-tweets`
  * publish npm package (1 day)
    * `pmgo-pokedex`
* LINE chatbot (5 days)
  * LINE console settings (Official account)
  * golang + LINE Messaging API SDK
    * `line-chatbot`
    * ngrok
  * Google Cloud Functions
  * RichMenu
    * `line-chatbot-richmenu`
* LINE LIFF - pokedex (5 days)
  * LINE console settings
  * React + TypeScript
    * `willow-pokedex`
    * routing
    * hooks
