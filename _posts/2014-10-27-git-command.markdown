---
layout: post
title:  "Git command I used most!"
date:   2014-10-27 08:16:42
categories: git
---
#### git config
    $ git config --global user.email 'mail_zlj a-t 163.com' ＃Set email
    $ git config --global user.email #Show email

#### git remote
    $ git remote add origin https://github.com/gazeldx/duokong.git #Add a remote URL
    $ git remote -v #List all remotes
    $ git remote set-url origin https://github.com/gazeldx/hutch.git
    $ git remote add upstream https://github.com/gocardless/hutch.git
    $ git push -u coding coding_local:master # Push to (coding master) remote branch from coding_local branch
    $ git push origin --delete develop # Delete a remote branch develop.
    $ git branch -d the_local_branch # Delete a local branch
    
#### git branch
    $ git branch # View local branches
    $ git branch -r # View remote branches
http://stackoverflow.com/questions/1519006/how-do-you-create-a-remote-git-branch
```
$ git checkout -b your_branch #这句话不会修改任何代码。git pull才会。your_branch的内容是独立的。如果是新建的，则等于现在的内容。
$ git push <remote-name> <branch-name># E.g: git push origin your_branch

http://stackoverflow.com/questions/5601931/best-and-safest-way-to-merge-a-git-branch-into-master

$ git checkout master
$ git pull origin master
$ git merge test
$ git push origin master

Run git fetch to update the "cache" of all remote repositories.
   $ git fetch
Run `$ git log <path/branch>` to view the log
```

#### pull request
https://help.github.com/articles/checking-out-pull-requests-locally/

https://help.github.com/articles/merging-a-pull-request/

```
$ git pull git://github.com/chenge/ruby-db-admin.git master # Merge from Pull request
```

#### git stash
    $ 可以把一些改动先藏起来。这样就可以成功pull 代码了，否则可能报错有冲突，不让你pull。pull完代码后，再git stash apply stash@{id}，就可以把本地藏起来的改动合入本地，可能需要你解决一下冲突。
    
Upload SSH public key

    $ pbcopy < ~/.ssh/id_rsa.pub