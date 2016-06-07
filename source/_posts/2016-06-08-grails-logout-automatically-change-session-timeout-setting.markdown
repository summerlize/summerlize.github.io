---
layout: post
title: "Grails: 設定 session 自動登出時間"
date: 2016-06-08 04:37
comments: true
categories: Grails
---

在 src/templates/war/web.xml 中修改 <session-timeout> 的設定即可，

時間單位為分鐘，預設值為 30。

	<session-config>
        <!-- 30 minutes -->
        <session-timeout>30</session-timeout>
    </session-config>