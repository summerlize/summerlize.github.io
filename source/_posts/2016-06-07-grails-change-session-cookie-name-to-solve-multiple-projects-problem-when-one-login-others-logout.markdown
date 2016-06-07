---
layout: post
title: "Grails: 更改 cookie 名稱以解決多專案無法同時登入問題"
date: 2016-06-07 22:15
comments: true
categories: Grails
---

Grails 同時 run 多專案時，若一專案登入帳號，另一專案便會自動登出，

這是由於 cookie 名稱相同所造成，

解決方式：

先安裝 templates `$grails install-templates`，

在 src/templates/war/web.xml 中新增自訂的名稱即可。

	<session-config>
        <cookie-config>
            <name>MY_SESSION_NAME</name>
        </cookie-config>
    </session-config>
