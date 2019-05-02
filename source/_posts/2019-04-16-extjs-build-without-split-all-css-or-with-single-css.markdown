---
layout: post
title: "ExtJS 6 css 設定編譯成單一檔案"
date: 2019-04-16 03:30
comments: true
categories: [ExtJS]
---

為了避免 IE 對 css 檔案過大的限制，

ExtJS 6 將編譯後的 app-all.css 檔拆成兩個檔案 app-all\_1.css app-all\_2.css，

如果仍想要保持編譯成單一 css 檔，

可在 app.json 中設定 css split 參數。

    "output": {
       "css": {
          "split": 18288
       },
    },


