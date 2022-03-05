---
title: 架設 HEXO 部落格的理由
date: 2022-02-22 23:01:49
categories: 心得
tags: [Hexo, Starter, Gulp]

---

![Photo by Michael Sum on Unsplash](https://i.imgur.com/gNM4kMT.jpg "Photo by Michael Sum on Unsplash")  


以前最先碰到的筆記工具是 HackMD，不過因為排版上必須使用 Markdown 語法，覺得不太方便，所以發現 Notion 後，就再也沒使用過 HackMD。

如果想要和其他人分享心得的話，發在 Medium 上，除了感覺比較正式之外，用 Notion 打好草稿後還是得處理麻煩的排版問題。
更重要的是，程式碼區塊真的很不易讀，因此開始尋找能夠客製化以及方便管理的工具。

曾經考慮過 Github 的個人頁面以及 WordPress，最後爬過幾位前輩的文章後，才決定使用 Hexo 和 Netlify 來建立個人部落格。

以後會把技術相關的文章放在 Hexo上；較為個人的、或是工作求職上的心得仍會發在 Medium 上。


---------------------------------------

## 踩雷經驗分享

有幾點想要另外分享給大家，以免踩到同樣的雷

以下是用 Windows 10 所建構的步驟，使用前也得先安裝最新的 nvm，請多加留意

### 注意 NodeJS 的版本

1. [以現在的 Hexo 6.0 為例](https://hexo.io/zh-tw/docs/#Minimum-required-Node-js-version)，LTS(Long Term Support) 為 `12.13.0`
2. 以我的例子來說，之前學習 JS 的時候是 `10.15.0` ，所以需要 Hexo 所推薦的 NodeJS 版本 
```bash 
$ nvm install 12.13.0
``` 
3. 安裝完畢後，得再輸入`$ nvm use 12.13.0` 才能夠使用剛安裝好的版本
4. 最後用 `$ node -v` 確認目前版本是否正確


### 字型的設定


![uglify1](https://i.imgur.com/tWEjwZC.jpg)
1. 看起來非常亂，是因為 [AOMORI](https://github.com/lh1me/hexo-theme-aomori) 主題本身有使用 Gulp 作為打包工具，將 CSS 和 JS 的部分 minify 與 uglify

2. 會優先以 AOMORI 內的 `custom.css` 而不是 `style.css` 作為樣板設定

---

### 入坑學習心得

感謝 Shelly 的文章，[可以讓我無痛入坑](https://icaughtacode-hexo-blog.netlify.app/2021/12/01/hexo%20%E7%84%A1%E7%97%9B%E5%85%A5%E5%9D%91%E5%9B%89/)，並且大力推薦 [Kyo 的心得](https://blog.kyomind.tw/my-markdown-writing-flow/)給同樣想使用 Notion 打草稿的朋友~


### 參考文章

1. [Hexo 無痛入坑囉](https://icaughtacode-hexo-blog.netlify.app/2021/12/01/hexo%20%E7%84%A1%E7%97%9B%E5%85%A5%E5%9D%91%E5%9B%89/) by [Sherry](https://icaughtacode-hexo-blog.netlify.app/about/)
2. [Notion + VS Code：我的 Hexo Markdown 寫作工作流](https://blog.kyomind.tw/my-markdown-writing-flow/) by [Kyo](https://blog.kyomind.tw/)
3. [[week 13] 前端工具之三 - gulp、webpack](https://hackmd.io/@Heidi-Liu/note-fe201-gulp-and-webpack#uglify-%E8%88%87-minify) by [Heidi-Liu](https://hackmd.io/@Heidi-Liu)
