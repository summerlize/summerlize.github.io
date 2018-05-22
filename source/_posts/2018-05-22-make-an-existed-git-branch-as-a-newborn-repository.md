---
layout: post
title: "Git: 將已存在的分支轉換為全新的程式庫 (不保留舊的 commit，但維持目前版本的程式碼)"
date: 2018-05-22 12:10
comments: true
categories: Git
---

GIT API 參考
 
>--orphan &lt;new_branch&gt;
>
>Create a new orphan branch, named <new_branch>, started from <start_point> and switch to it. The first commit made on this new branch will have no parents and it will be the root of a new history totally disconnected from all the other branches and commits.
>
>The index and the working tree are adjusted as if you had previously run "git checkout <start_point>". This allows you to start a new history that records a set of paths similar to <start_point> by easily running "git commit -a" to make the root commit.
>
>This can be useful when you want to publish the tree from a commit without exposing its full history. You might want to do this to publish an open source branch of a project whose current tree is "clean", but whose full history contains proprietary or otherwise encumbered bits of code.
>
>If you want to start a disconnected history that records a set of paths that is totally different from the one of <start_point>, then you should clear the index and the working tree right after creating the orphan branch by running "git rm -rf ." from the top level of the working tree. Afterwards you will be ready to prepare your new files, repopulating the working tree, by copying them from elsewhere, extracting a tarball, etc.

<br />

	# checkout 成不繼承舊 commit 的全新分支(latest_branch)，
	$ git checkout --orphan latest_branch
	
	# 加入所有檔案
	$ git add -A

	# commit 初始記錄
	$ git commit -am "init project"
	
	# 刪除已存在的 master 分支
	$ git branch -D master
	
	# 將目前分支 latest_branch 重新命名為 master
	$ git branch -m master

	# 強制更新遠端程式庫 master 分支
	$ git push -f origin master