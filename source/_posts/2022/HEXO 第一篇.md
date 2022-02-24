---
title: HEXO 第一篇
date: 2022-02-22 23:01:49
categories: 隨筆
tags: [Hexo, starter]

---

## 為什麼改用 Hexo

![圖 1](https://i.imgur.com/gNM4kMT.jpg)  

以前剛接觸筆記軟體的時候，最先碰到的是 HackMD ，再來才是 Notion。

為何討厭 HackMD ？因為一邊是原生的語法，而另一邊是渲染過的畫面，這樣寫起來跟寫 Code 很像！
我只是想寫點文章偶爾發點廢文，而不想再另外刻畫面啊！

### 那麼我的需求是什麼？

1. 簡單來說就是不必輸入 Markdown 符號，也能輕鬆寫文章。
2. Medium 真的不適合放技術文章。
程式碼區塊和排版呈現這兩項也被不少前輩所詬病，就算把 Notion 的草稿丟上去，還是有很多繁雜的排版工作得處理。身為一個軟體工程師，這不能忍的吧？！

### 入坑學習心得

感謝 Shelly 的[文章，可以讓我無痛入坑](https://icaughtacode-hexo-blog.netlify.app/2021/12/01/hexo%20%E7%84%A1%E7%97%9B%E5%85%A5%E5%9D%91%E5%9B%89/)，並且大力推薦同樣想使用 Notion 打草稿的朋友

不過有以下幾點想要另外分享給大家，以免踩到同樣的雷

以下是用Windows 10 所建構的步驟，使用前也得先安裝最新的 nvm

1. 注意 NodeJS 的版本：
    1. [以現在的 Hexo 6.0 為例](https://hexo.io/zh-tw/docs/#Minimum-required-Node-js-version)，LTS(Long Term Support) 為 12.13.0
    2. 以我的例子而言，之前學習 JS 的時候是 10.15.0 ，所以輸入  `$ nvm install 12.13.0` 以安裝目前 Hexo 所推薦的版本
    3. 安裝完畢後，得再輸入`$ nvm use 12.13.0` 才能夠使用
    4. 最後用 `$ node -v` 確認目前版本是否正確
2.

### **_config.yml**

**指的是根目錄下的，而不是 `theme/` 底下的**

可以設定全站的呈現方式

### 參考文章

1. [Hexo 無痛入坑囉](https://icaughtacode-hexo-blog.netlify.app/2021/12/01/hexo%20%E7%84%A1%E7%97%9B%E5%85%A5%E5%9D%91%E5%9B%89/) by [Sherry](https://icaughtacode-hexo-blog.netlify.app/about/)
2. [Notion + VS Code：我的 Hexo Markdown 寫作工作流](https://blog.kyomind.tw/my-markdown-writing-flow/) by [Kyo](https://blog.kyomind.tw/)
