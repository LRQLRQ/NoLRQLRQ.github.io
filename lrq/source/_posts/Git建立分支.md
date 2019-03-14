---
title: Git的远程建立分支
tags: Git
---

2018-12-03
项目需要给邮政那边一个版本，所以需要给他们进行定制。因此就需要在现在的git仓库上新建一个分支，在此列出需要的几个命令。

`git branch` :列出本地的分支，当前分支会标有`*`号
`git branch -r` :列出远程的分支
`git branch -a` :列出所有本地和远程的分支

## 首先新建一个本地分支：
`git checkout -b lrqTest`
上面这行代码代表新建一个分支并切换到分支内，等同于下
`git branch lrqTest`
`git checkout lrqTest`

## 第二步，将新建的本地分支push到远程服务器，远程分支名字可以随便起
`git push origin lrqTest:remoteTest`
其中`lrqTest`是本地的分支名,`remoteTest`是远程服务器的分支名，可以随便起名

到此，就建立了远程分支。

---

## 如果想要删除远程分支
`git push origin  :lrqTest`
意思是将一个空的分支push到远程名为lrqTest的分支，就是相当于删除.
也可以使用此命令：
`git pust origin --delete lrqTest`

---

## 如果某个用户想要拉取远程的分支
1. 直接使用`git checkout -b 本地分支名 origin/远程分支名`
这种方式会直接在本地新建分支，并且切换到该分支。
并且，这种方式会将新建立的本地分支和远程分支建立**映射关系**
2. 使用`git fetch origin 远程分支名:本地分支名`
这种方式建立的分支不会切换到新建的分支，需要自己手动进行checkout
而且，这种方式建立的本地分支**不会和远程分支建立映射关系**


---

##建立本地分支和远程分支之间的映射关系
建立了映射关系之后，就不需要在每次`git pull` 和 `git push`的时候指定从远程的哪个分支拉取合并和推送到哪个分支。

使用`git branch -vv`查看本地分支和远程分支的映射关系
如果没有映射关系，就需要自己**手动建立**：
`git branch -u origin/分支名`
或者
`git branch --set-upstream-to origin/分支名`
origin为git地址的标志，可以建立当前分支与远程分支的映射关系

**撤销本地分支与远程分支的映射关系**
`git branch -unset-upstream` 就撤销掉了与远程分支的映射关系






