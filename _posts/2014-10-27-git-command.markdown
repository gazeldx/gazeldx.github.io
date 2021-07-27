---
layout: post
title:  "Git command I used most!"
date:   2014-10-27 08:16:42
categories: git
---

#### 简单的git代码提交流程
* 平时开发，在本地建一个'branch 你的姓名'，如'branch zhangsan'，并把开发代码提交到remote的'branch 你的姓名'。测试确认无问题后合入master
* 'branch master'作为产品更新的唯一branch
* 如果涉及到某个功能需要多人合作完成，这时把自己已经完成，并无不可运行错误的代码，从'branch 你的姓名'合入到'branch develop'中，等develop测试完毕，合入到master中

**可能涉及的一些git操作**
{% highlight bash %}
$ git reset --hard a_commit # 强行回滚到某个旧版本，这在很多时候都很好用，比如分支错乱git rebase又不会用的时候
$ git fetch # 如果git remote有更新，必须要fetch一下，否则可能找不到最新的commits
$ git cherry-pick a_commit # 把某个commit放到当前本地branch
{% endhighlight %}

#### git config
{% highlight bash %}
$ git config --global user.email 'mail_zlj a-t 163.com' ＃Set email
$ git config --global user.email #Show email
$ git config --global user.name
$ git config user.email # If we don't use the `--global`, that means we set in this git project we use another user.email. `git commit` and `git log` to see what difference. Then if it is displayed in GitHub, we can see another user is contributing if this email is an account of GitHub. 
{% endhighlight %}

#### git remote
{% highlight bash %}
$ git remote add origin https://github.com/gazeldx/duokong.git #Add a remote URL
$ git remote -v #List all remotes
$ git remote set-url origin https://github.com/gazeldx/hutch.git
$ git remote add upstream https://github.com/gocardless/hutch.git
$ git ls-remote # list remote branches
{% endhighlight %}

#### git push
{% highlight bash %}
$ git push -u coding coding_local:master # Push to (coding master) remote branch from coding_local branch
{% endhighlight %}

#### git branch
http://nvie.com/posts/a-successful-git-branching-model/

{% highlight bash %}
$ git branch # View local branches
$ git branch -r # View remote branches
$ git branch -d the_local_branch # Delete a local branch
$ git push origin --delete develop # Delete a remote branch develop.
如何在remote上创建一个branch？下面两句即是，参考自：http://stackoverflow.com/questions/1519006/how-do-you-create-a-remote-git-branch
$ git checkout -b your_branch # 切换到your_branch，如果your_branch不存在就创建它。这句话不会修改任何代码。
$ git push <remote-name> <branch-name> # E.g: git push origin your_branch
{% endhighlight %}

{% highlight bash %}
如何合入到master？下面几句即是，参考自：http://stackoverflow.com/questions/5601931/best-and-safest-way-to-merge-a-git-branch-into-master
$ git checkout master
$ git pull origin master
$ git merge test
$ git push origin master
$ git log <path/branch>
{% endhighlight %}

#### git stash
{% highlight bash %}
$ 可以把一些改动先藏起来。这样就可以成功pull代码了，否则可能报错有冲突，不让你pull。pull完代码后，再git stash apply stash@{id}，就可以把本地藏起来的改动合入本地，可能需要你解决一下冲突。
$ git stash list
$ git stash show -p stash@{0} # 查看stash@{0}内容.stash@{0}可用0替换。下同。
$ git stash apply stash@{0} # 将stash@{0}内容恢复到当前版本
{% endhighlight %}

#### pull request
{% highlight bash %}
https://help.github.com/articles/checking-out-pull-requests-locally/
https://help.github.com/articles/merging-a-pull-request/
$ git pull git://github.com/chenge/ruby-db-admin.git master # Merge from Pull request
{% endhighlight %}

#### git rebase
https://gitbook.tw/chapters/rewrite-history/merge-multiple-commits-to-one-commit.html
{% highlight bash %}
$ git log --oneline
$ git rebase -i <commit_id_or_branch_name> # commit_id_or_branch_name一般会选择用master。进入后，在界面中可以reorder(直接修改行的位置), squash(把若干commit合成一个)或（直接删除掉不需要的commit就可以删除commit了）
$ 假如现在有两个分支同时开发，都基于master。现在branch_A先合入到master中了，这时在branch_B中应该git rebase -i master，然后`:q!`。如果有冲突，就修复冲突，然后把修复后的文件`git add`进来，然后
$ git rebase --continue # 可能还有冲突，继续解决冲突，并git rebase --continue。最终会使得branch_B中的所有提交都在branch_A之后。所有人都这样做就完美了。
$ git push origin your_branch -f # 要加-f才能提交成功
{% endhighlight %}

#### Git tag
```
git tag -a v1.0.17 -m "My version v1.0.17"
git push origin v1.0.17
git push --delete origin v1.0.23
```

#### Permission issues
Run `git push origin PROJ-674` but got:
```
ERROR: Repository not found.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights, and the repository exists.
```

This repo in GitHub is a private repository.
First, `ls ~/.ssh` to see if there are `id_rsa` and `id_rsa.pub` pairs.
Actually, you can generate many pairs for different accounts by `ssh-keygen -t rsa`.

Then add the `id_rsa.pub` or something like `id_rsa_2.pub` to GitHub [SSH and GPG keys](https://github.com/settings/keys).

Then run `ssh-add ~/.ssh/id_rsa_2`.
