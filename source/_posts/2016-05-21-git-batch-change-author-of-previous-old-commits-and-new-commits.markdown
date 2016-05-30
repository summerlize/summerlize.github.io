---
layout: post
title: "Git: 批量變更已 commit 的創作者 (author) 及欲 commit 的創作者"
date: 2016-05-21 09:42
comments: true
categories: Git
---

###變更過去已 commit 的 author

首先在 project 中建立一個 sh 檔案

	$ cd yourProjectPath
	$ touch gitFilterBranch.sh

接著將下方語法複製到檔案中，並且修正

`OLD_EMAIL` 為欲取代的舊的作者信箱、

`NEW_NAME` 為欲變更的新的作者名、

`NEW_EMAIL` 為欲變更的新的作者信箱。

{% codeblock git filter branch - gitFilterBranch.sh lang:sh %}

git filter-branch -f --env-filter '
OLD_EMAIL="old@mail.com"
NEW_NAME="newUser"
NEW_EMAIL="new@mail.com"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$NEW_NAME"
    export GIT_COMMITTER_EMAIL="$NEW_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$NEW_NAME"
    export GIT_AUTHOR_EMAIL="$NEW_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags

{% endcodeblock %}

輸入指令 `chmod +x gitFilterBranch.sh` 將檔案變更為可執行檔

輸入指令 `./gitFilterBranch.sh` 執行 shell 檔案

此時就可以看到過去的 commit author 改變囉～

如果發現有些 branch 還是舊的 author ，

先切換至該分支，再重新 run 一次 sh 就行了。

###變更未來欲 commit 的 author

在 project 中使用以下指令變更

	$ cd yourProjectPath
	$ git config user.name "summerlize"
	$ git config user.email "summerlize@gmail.com"