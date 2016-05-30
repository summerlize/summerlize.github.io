---
layout: post
title: "Grails + iReport: PDF 繁體中文無法顯示解決方法"
date: 2014-04-26 02:59
comments: true
categories: [iReport, Grails]
---
Grails 版本: 2.3.5

iReport 版本: 5.5.1

iReport 在產生 PDF 檔一直都有中文顯示異常的問題，

此次要記錄的是在 Grails 中的解決辦法，

首先在 iReport 中將顯示中文的欄位屬性設定如下，

**Pdf Font name is now deprecated. -> MSung-Light**

**Pdf Embedded ->勾選**

**Pdf Enconding -> UniCNS-UCS2-H(Chinese traditional)**

![iReport Field Properties](https://farm3.staticflickr.com/2930/13987705336_f0d5782478_n.jpg)

再到 iReport 安裝路徑中複製出 iTextAsian.jar ，

夏天安裝在 mac 中路徑如下，找不到的話應該可以從網路上下載。

/Applications/Jaspersoft iReport Designer.app/Contents/Resources/ireport/ireport/libs

![iTextAsian @iReport](https://farm6.staticflickr.com/5331/13987706016_05fe74337a.jpg)

接著將檔案丟到 Grails Project 中的 lib 資料夾下

![iTextAsian @Grails](https://farm8.staticflickr.com/7374/13987705366_2f5173dc86.jpg)

這樣輸出的 PDF 就可以顯示中文摟～ ：）



