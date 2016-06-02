---
layout: post
title: "Facebook 按鈕造成 The domain of this URL isn't included in the app's domains... 錯誤 如:讚(like)、分享(share)、登入(login)...等"
date: 2016-06-03 07:06
comments: true
categories: [Facebook, Octopress]
---

最近突然發現網頁出現錯誤訊息：

<font color="red">無法載入網址: The domain of this URL isn't included in the app's domains. To be able to load this URL, add all domains and subdomains of your app to the App Domains field in your app settings.</font>

夏天的 blog 中有使用 addthis 提供的按鈕服務，檢查後發現是 facebook 的按鈕造成的，

查了一些資料發現應該是要將網頁與 facebook 的開發管理網站做個連結，

其實不做的話按鈕還是可以正常使用，只是煩人的訊息有點刺眼 XDD

首先登入 [Facebook 開發者網站](https://developers.facebook.com) ，

接著新增一個應用程式，在左邊選單點選**設定**選項，

在右邊畫面下方可以找到**+新增平台**按鈕，點下後選取網頁類型並輸入網站網址，

完成後按下**立即開始**，將程式碼加入網頁中的 body 區塊即可。



