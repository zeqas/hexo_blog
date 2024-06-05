---
title: 圖片壓縮
date: 2024-06-05 17:05:35
categories: 筆記
tags: [AWS, webp, Lambda]
---

# 圖片壓縮

目標：將 png 圖片轉換成 webp 格式，以提升網頁瀏覽速度並且減少伺服器 loading

注：這是當初工作的個人筆記，已將敏感部分替換掉

參考自以下網站：

1. **[使用 AWS Lambda 提高网站图片加载速度 1X 倍](https://www.bmpi.dev/dev/aws-lambda-edge-img-to-webp)**

2. [圖片縮放指南](https://awslabs.github.io/aws-cloudfront-extensions/zh/extension-repository/resize-image/)

## 初步試作

步驟：

1. 建立一個放圖片的 S3 之後，到 CloudFront 新增一個 distribution 指定剛剛建好的 S3

2. 建立新的 OAI、更新 S3 的存取政策

3. 更新 Action 為 `s3:ListBucket`，更新 Resource 為 S3 的 ARN

4. 到 [SAR](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:1111111111:applications/resize-picture)(圖片縮放的 Repo)建立 Lambda Edge
   建立後指定剛剛新增的 CloudFront，CloudFront Event 要選擇 Origin Response

問題：

1. 如果有更新程式碼，就要佈署新版本到 Lambda@edge

2. 單純照著圖片縮放指南的步驟，可以成功地重新裁切圖片至指定大小，但是沒辦法將圖片轉換為其他格式

3. 有修改了 `app.js` 的 Content Type，但是無法解析該請求

### 環境問題

1. 發現 SAR 只有 resize ，沒有轉換圖片的功能，但也有可能是 Origin Request 沒辦法修改，所以拿不到 Webp 的圖片

2. 安裝 CDK 發現 Node.js 過舊，所以要更新版本（但是不知道其他專案有沒有限定版本的 Node）

```bash
sudo rm -rf /usr/local/bin/npm /usr/local/share/man/man1/node* ~/.npm
sudo rm -rf /usr/local/lib/node*
sudo rm -rf /usr/local/bin/node*
sudo rm -rf /usr/local/include/node*

sudo apt-get purge nodejs npm
sudo apt autoremove

# 安裝 node
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

### 重新評估執行步驟

如果只要照著步驟做，並且將 Lambda 的程式碼更新的話，還要做哪些事情？

1. 因為目前網站的 S3 包括人物、簡報、白皮書與廣告的原始圖片，所以之後要建立一個新的 S3，專門放 webp 格式的圖片，並且將舊的圖片搬過去

2. S3 有 ACL 的設定，所以「公有存取權」有關 ACL 的設定要取消勾選

3. S3「物件擁有權」也要指定為「物件選擇器」

### 前輩提供的建議與步驟

前輩昨天找到另一篇[文章](https://www.adrenalinmedia.com.au/webp-with-cloudfront-cdn-and-s3/)，可以直接將圖片轉成 webp。

（但是我認為這樣很麻煩，因為之後可能還要寫排程，定期更新圖片，也不能及時處理需求，長遠來看並不是好方法）

圖片轉檔功能：

- AWS 服務：

  - 新增：S3 + CloudFront + Lambda

  - 舊的 S3 _搬到新的 s3_

  - 新的 cloudfront 連新的 s3

  - Lambda 的部署、程式碼上傳

  - IAM Role 的設定

  - 測試機、正式機使用的 cloudfront 跟 s3 是分開的，所以請 DEV 建置的話，要說明清楚

- 後台：

  - 可以用指令 批次修改 檔案紀錄資料表 圖片的 disk、url 值

  - 新增圖片用的 disk(filesystems.php)

  - 修改圖片上傳的 disk、.env(AWS_BUCKET、AWS_URL)

  - 檢查圖片以外的檔案上傳功能是否正常運作

  - 檢查圖片上傳是否正常丟到新的 s3 位置

- API：

  - 修改圖片副檔名

  - 建議此功能更新的順序：

    1. 先處理 aws 的服務

    2. 再更新後台程式：

       - 設定 .env

       - 清 config

       - 批次修改 檔案紀錄資料表 中，disk、url 欄位

    - 最後更新 API 程式

### 瓶頸

我不知道要怎麼更改跟圖片相關的設定，以下是我目前知道的事情：

1. 要建立一個新的 S3，並且使用 Lambda@edge 跟 CloudFront 處理圖片；

以下是我覺得也許做得到的事情：

1. 既然舊的 S3 已經有圖片了，那麼能不能在取得圖片時，加上一層 Lambda 來轉換成 webp？
   因為它只會轉換指定 URI 的圖片，其他前綴不同的圖片則不會被轉換

2. 以正式機為例，目標是解析特定前綴為 `/site-name/image/` 的圖片，新的圖片則會跟舊圖片放在同一個資料夾底下

3. 建立新的 CloudFront ，選擇指定的 S3 後，要另外建立一個新的 OAI

4. 透過 [SAR](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:111111111111:applications/resize-picture) 建立 Lambda

5. 打開函數的壓縮檔，更改 `app.js` 中的 S3 名稱，再上傳修改後的壓縮檔

6. 佈署至 Lambda@edge，CloudFront Event 選擇 “Origin Request”

### 與 DEV 說明

因為圖片是透過 CloudFront 和 Lambda 處理，所以在第一次接收到圖片的請求時，會在 S3 生成並回傳新的 .webp 圖片

佈署之後，如果前端 call API 後，沒有在圖片的 URL 後面加上 .webp 的副檔名，就不會取得 webp 圖片

所以在前端沒有更新，但已經佈署好 Lambda 的情況下，我們可以在既有的圖片 URL 後面加上 .webp 測試

因此我覺得不會影響到目前的平台

由於這篇[文章](https://awslabs.github.io/aws-cloudfront-extensions/zh/extension-repository/resize-image/)的目標是新建一個服務，所以我想補充在已有 S3 和 CloudFront 的情況下，步驟之間的差異

Lambda：

1. 使用 [SAR](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:418289889111:applications/resize-picture) 建立 Lambda，參數隨意，因為會被覆蓋

2. 透過 CloudFormation 佈署完成後，點選 Resource 中的 **ResizeLambdaEdgeFunction**

3. 打開函數的壓縮檔，更改 `app.js` 中的 S3 名稱，再上傳修改後的壓縮檔，以更新程式碼

4. 更新程式碼後，點選 操作->佈署至 Lambda@edge

5. 建立新的 CloudFront Trigger：Distribution 選擇與 S3 串接的 CloudFront；
   Event 選擇 “Origin Response”，勾選佈署到 Lambda@edge 後按部署鍵

CloudFront：

1. 在 S3 串接的 CloudFront 建立行為，有以下三個參數需要設定

2. 路徑模式：填上 `/site-name/image/*`
   來源：選擇圖片的 S3
   功能關聯：來源回應 → 函數類型，選擇 Lambda@edge 並且填上對應的 ARN

### DEV 說明現況

DEV 說因為 cloudfront 跟 Lambda@edge 的帳號不同，所以沒辦法串接

如果在 cloudfront 的帳號佈署 lambda，且 cloudfront 已和另一個帳號的 S3 串接的情況下，應該能夠正常運作才對

## 總結和懶人包

Lambda：

1. 使用 [SAR](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:418289889111:applications/resize-picture) 建立 Lambda，參數隨意，因為會被覆蓋

2. 透過 CloudFormation 佈署完成後，點選 Resource 中的 **ResizeLambdaEdgeFunction**

3. 打開函數的壓縮檔，更改 `app.js` 中的 S3 名稱，再上傳修改後的壓縮檔，以更新程式碼

4. 更新程式碼後，點選 操作->佈署至 Lambda@edge

5. 建立新的 CloudFront Trigger：Distribution 選擇與 S3 串接的 CloudFront；
   Event 選擇 “Origin Response”，勾選佈署到 Lambda@edge 後按部署鍵

CloudFront：

1. 在 S3 串接的 CloudFront 建立行為，有以下三個參數需要設定

2. 路徑模式：填上 `/site-name/image/*`
   來源：選擇圖片的 S3
   功能關聯：來源回應 → 函數類型，選擇 Lambda@edge 並且填上對應的 ARN

S3：

1. 由於公司的 S3 和 Cloudfront 分屬不同帳號，因此佈署 Lambda 之後，要再到 S3 的許可政策設定權限

```yaml
{
  "Version": "2012-10-17",
  "Statement":
    [
      {
        "Sid": "AllowLambdaAccess",
        "Effect": "Allow",
        "Principal": { "AWS": "arn:aws:iam::{$ACCOUNT_ID}:role/{$ROLE_NAME}" },
        "Action": ["s3:GetObject", "s3:PutObject"],
        "Resource": "arn:aws:s3:::{$BUCKET_NAME}/*",
      },
    ],
}
```

## 後記

1. 這個做法跟這篇部落格文章不同，它分成了 Viewer Request 和 Origin Response 兩個函數，但我們全放在同一個函數，所以可能會比較慢一點

2. 目前平台需要轉成 webp 的圖片總數量不超過萬張，若未來需要將全部圖片轉換成 webp ，粗略估計也不會超過十萬張；而上述的部落格文章提到的是每百萬張的成本為 90 元人民幣，由此可見此服務的效益是很不錯的

3. 面試被問倒後才發現，當初其實沒有考慮儲存空間，只有想著要把自動轉檔的功能做出來 😓，沒有和前輩們討論後續維護。（但印象中是說 S3 有生命週期，讓 DEV 之後去煩惱就好 🤔？）
