---
layout: post
title: "Octopress: 變更 master 分支中 commit 的創作者(author)"
date: 2016-05-30 04:46
comments: true
categories: [Octopress]
---

使用 Octopress 基本上會有兩個分支 source 和 master ，

其中 source 是由使用者自行管理 git，

而 master 則是透過 rake deploy 自動管理，

想要變更 source 分支的 commitments author ，

可以參考這篇 [Git: 批量變更已 commit 的創作者(author)及欲 commit 的創作者](http://summerlize.github.io/blog/2016/05/21/git-batch-change-author-of-previous-old-commits-and-new-commits) ，

變更之後再將 origin source 刪除重新 push 即可，

但如果是 master 分支照做之後，下次 rake deploy 時又會以舊的作者重新 commit ，

並顯示錯誤訊息：

	error: failed to push some refs to 'git@github.com-xxxxxxxx'
	hint: Updates were rejected because the tip of your current branch is behind
	hint: its remote counterpart. Integrate the remote changes (e.g.
	hint: 'git pull ...') before pushing again.
	hint: See the 'Note about fast-forwards' in 'git push --help' for details.

此時照著訊息做是無效的，

必須用 `rake setup_github_pages` 重新生成 _deploy 資料夾，如此舊作者的 commit 紀錄才會被刪除。

ps. 在這之前還是需要先把 octopress 的 git user 變更設定為新作者的喔，請參考 [前面提到的文章](http://summerlize.github.io/blog/2016/05/21/git-batch-change-author-of-previous-old-commits-and-new-commits) 。

一般應該是做到這邊就能完成，但如果有設定過 git global user 則會無法成功，

rake deploy 會使用 global user 進行 commit 。

使用指令 `git config --global user.name` 可以確認是否有設定過，

如果有則需在 octopress 資料夾中的 Rakefile 加入兩行指令，來變更 _deploy 資料夾 git user的設定，

{% codeblock Rakefile lang:rb %}
cd "#{deploy_dir}" do
    system "git init"
    	
    system "git config user.name \"summerlize\""                //加入此行
    system "git config user.email \"summerlize@gmail.com\""     //加入此行
    
    system "echo 'My Octoprese is coming soon &hellip;' > index.html"
    system "git add ."
    system "git commit -m \"Octopress init\""
    system "git branch -m gh-pages" unless branch == 'master'
    system "git remote add origin #{repo_url}"
    rakefile = IO.read(__FILE__)
    rakefile.sub!(/deploy_branch(\s*)=(\s*)(["'])[\w-]*["']/, "deploy_branch\\1=\\2\\3#{branch}\\3")
    rakefile.sub!(/deploy_default(\s*)=(\s*)(["'])[\w-]*["']/, "deploy_default\\1=\\2\\3push\\3")
    File.open(__FILE__, 'w') do |f|
      f.write rakefile
    end
  end
{% endcodeblock %}


並且重新使用 `rake setup_github_pages` 重新生成 _deploy 資料夾，

一般會有這樣的設定大部分是因為有一個以上的 github 帳號，

因此在 `rake setup_github_pages` 的時候也需輸入正確的 url 才能使用對應的 ssh-key，

	$ rake setup_github_pages
	Enter the read/write url for your repository
	(For example, 'git@github.com:your_username/your_username.github.io.git)
	           or 'https://github.com/your_username/your_username.github.io')
	           
	Repository url: git@github.com-personal:personal/personalproject.github.io.git   //指定 ssh-key 的 url

可參考此篇 [同時使用多個 GitHub 帳號與多個 SSH 認證金鑰之設定方式](http://summerlize.github.io/blog/2016/05/21/multiple-github-accounts-with-different-ssh-key/) 。

最後再run `rake_deploy` 就能看到 commit author 變成新的了～








