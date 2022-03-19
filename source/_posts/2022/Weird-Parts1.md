---
title: Weird Parts 型別轉換與創造物件
date: 2022-03-19 00:36:30
categories: 筆記
tags: [JavaScript, Understanding the Weird Parts, coercion, JSON, Object Literals, Starter]
---


![圖 1](https://i.imgur.com/FODa2Mx.jpg)  


經過將近三個月的沉澱，以及觀察已經上岸的同學們，深刻體悟到一個大盲點：學習新框架或是工具，甚至是做 Side Project 都很好，但是如果最基礎的 JavaScript 都不甚理解的話，日後無論學習或是求職必定是事倍功半。

於是在上禮拜，終於購買了鼎鼎大名的 **[JavaScript: Understanding the Weird Parts](https://www.udemy.com/course/understand-javascript/) ，**並且深深地感到後悔。

後悔為什麼不早點買。

所以預計接下來的筆記內容會以課程內容為主。

---
## 強制轉型

1. ### `console.log(3 < 2 < 1)` 和 `console.log(1 < 2 < 3)` 會印出什麼?
    3 < 2 會被轉成 `false`，而當 `false < 1` 時
    會將 `false` 轉回 `0`，因此第一個答案會是 `true`

    同樣地， 1 < 2 會被轉成 `true`，
    `true < 3` 會讓 `true` 又轉成 `1`，第二個答案也會是 `true`


2. ### 如果有兩個 library 或 framework 設定的全域物件名稱相同
    會以最後呼叫的檔案中的變數為主。
    ![globalObject1.png](https://i.imgur.com/iYuDUnI.png)  
    
    所以看到像上圖這樣呼叫 window 物件，是為了避免被後面的變數取代，利用強制轉型，使 `libraryName` 變成 `“Lib1”` ，再利用[邏輯運算子](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Guide/Expressions_and_Operators#%E9%82%8F%E8%BC%AF%E9%81%8B%E7%AE%97%E5%AD%90)的特性讓 `window.libraryName = “Lib1”` 
    
---
## 創造物件

1. [Object() constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/Object) 物件建構子
    1. 無實例，以下兩個範例有相同的結果
        
        ```jsx
        let person = new Object // 範例1
        
        let person = {} // 範例2
        ```
        
    2. [Object.create()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 已有實例，則可套用該物件的格式建立另一個物件
        
        
2. Object Literals Syntax 物件實字語法
    1. [Property accessors](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors) 告訴我們點記法和中括號記法兩者的結果是相同的，但在創立物件和屬性的時候，如下使用語法都會出現錯誤 `Uncaught TypeError: Cannot set property 'firstName' of undefined。`
        
        ```jsx
        const profile = {}
        profile.name.firstName = 'Casper'
        profile[name][firstName] = 'Casper'
        ```
        
        因為運算子的相依性，會被認定為 `undefined` 
        所以需要先建立一個 `profile.name = {}` 
        
        ```jsx
        const profile = {}
        profile.name = {} // 加上這行
        profile.name.firstName = 'Casper'
        ```
        
    2. 利用物件實字語法就不會出現以上的情況，也更加簡潔與方便。
    在 JS 中沒有「命名空間」，但是可以像下面的例子一樣，建立一個類似 C++ 中類似命名空間的**容器，**避免命名衝突。
        
        ```jsx
        const profile = {
          name: {
        	  firstName: 'Casper'
        	}
        }
        ```
        
    3. JSON 是受到物件實字語法的便利性所建構的一種格式，主要簡化了許多不必要的符號，得以讓檔案縮小並提升傳輸效率。
    也因為 JSON 比起物件實字語法要來得嚴格，可以快速分辨兩者之間的差異。
    最主要的差異是在最外層需要用引號包裝，鍵也必須是以**雙引號`""`包裝的**字串。
        
        ```jsx
        const objectLiteral = {
            firstname: 'Casper',
            isAProgrammer: true
        }
        
        console.log(JSON.stringify(objectLiteral))
        
        const jsonValue = JSON.parse('{ "firstname": "Casper", "isAProgrammer": true }')
        
        console.log(jsonValue)
        ```
      
      ![JsonExample](https://i.imgur.com/bmzrBt3.png)  

        
    

## 參考資料

1. [Why the object[bar] has the same output as object[foo]?](https://stackoverflow.com/questions/51153627/why-the-objectbar-has-the-same-output-as-objectfoo)
2. [JSON.parse() - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)