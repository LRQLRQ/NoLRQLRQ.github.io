---
title: Git的简单使用
tags: git
---


提交版本：
git add xxx
git commit -m “注释”

查看修改了什么东西：git diff xxx

查看历史记录：git log	精简版:git log --pretty=oneline
版本回退到上一个版本：git reset --hard HEAD^
版本回退到上100个版本：git reset --hard HEAD~100

恢复之前的版本：
1.获得之前的版本号git reflog   
2.git reset --hard 版本号       来恢复
![avatar](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014061202.jpg)

修改了文件之后，还未git add的情况下，想回复之前的状态，可以输入:git checkout -- 文件名 

文件修改后，还没有add放到暂存区，使用撤销修改就回到和版本库一模一样的状态。
另外一种是文件已经add放入暂存区了，接着又作了修改，撤销修改就回到添加暂存区后的状态。


删除了文件之后，还未commit提交的情况下，想回复仓库之前的状态，可以输入 git checkout -- 文件名
经常输入git status 来查看文件的状态

把本地的仓库上传到github上：
1.在github新建一个仓库
2.输入git remote add origin 地址

把本地库的内容推送到远程，使用 git push命令，实际上是把当前分支master推送到远程
由于远程库是空的，我们第一次推送master分支时，加上了 –u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。推送成功后，可以立刻在github页面中看到远程库的内容已经和本地一模一样了

从现在起，只要本地作了提交，就可以通过如下命令：
git push origin master

克隆远程库：
假如远程库有新的内容了，我想克隆到本地来，如何克隆呢
使用命令git clone克隆一个本地库
git clone -b 分支名 地址


##创建与分支合并##

git checkout 命令加上 –b参数表示创建并切换，相当于如下2条命令

git branch dev  //创建分支

git checkout dev    //切换分支

##总结创建与合并分支命令如下：##

   查看分支：git branch

   创建分支：git branch name

   切换分支：git checkout name

创建+切换分支：git checkout –b name

合并某分支到当前分支：git merge name

删除分支：git branch –d name


##如何解决冲突？##
在merge时，主干和分支不同的情况下，会产生冲突
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，其中<<<HEAD是指主分支修改的内容，>>>>>fenzhi1 是指fenzhi1上修改的内容

如果我想查看分支合并的情况的话，需要使用命令 git log

##分支策略##


首先master主分支应该是非常稳定的，也就是用来发布新版本，一般情况下不允许在上面干活，干活一般情况下在新建的dev分支上干活，干完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来。

##BUG分支##
在开发中，会经常碰到bug问题，那么有了bug就需要修复，在Git中，分支是很强大的，每个bug都可以通过一个临时分支来修复，修复完成后，合并分支，然后将临时的分支删除掉。


