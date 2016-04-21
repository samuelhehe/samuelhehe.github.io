---
layout: post
title: git学习笔记
category: 工具
tags: git
keywords: 工具,git notes
description: git 学习笔记， 学完了，记录一下，备忘。
---

> 学习时候记录的，比较乱，但是不耽误看，简单易懂。找不到 ctrl+f

## github学习笔记 

$ mkdir learngit  // 创建目录

$ cd learngit  // 进入目录

$ pwd   // 当前目录完整目录

$ git init  创建库  ls -ah 可已查看隐藏目录.

git add . / file  添加文件到respostory 

$ git add file1.txt

$ git add file2.txt file3.txt

$ git commit -m "add 3 files." 可多次使用.

```
git commit m " hello world "  文档提交到库
```

git status  库的当前状态

git diff    文档变化内容 

git log  由远及近的历史更新信息.

git log -pretty=oneline

git reset --hard head^  回退至上一个版本 // head^^ 上上个版本 

git reset -- hard "3366baed"  // 回退至指定commitid 的库状态  . 

git 的当前库信息存放至 head 文档中  使用指针来指示指定的版本编号,方便版本之间的切换工作.

git  relog  查看命令历史, 可以查看各个版本的信息,包含有标示性的commit id 

git reset  head abc.txt  可以将暂存区的修改撤销掉.

git checkout -- abc.txt  可以丢弃工作区的修改.


* 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。

* 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

* 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

$ git remote add origin git@github.com:michaelliao/learngit.git  // 添加远端库至本地

git push -u origin master  提交至远端 第一次提交,包含所有分支

git push origin master  提交最新库至远端


要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；

关联后，使用命令git push -u origin master第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！

git checkout -b dev  // 创建新的分支 dev  -b  表示创建并切换,

git branch dev  // 创建新分支

git checkout  dev    // 切换至新分支

git branch  // 查看当前所有分支命令 . 当前分支前边会有 * 号

git checkout  master   切换回master 分支

git merge master   合并指定分支到当前

也就是直接把master指向dev的当前提交，所以合并速度非常快。// 修改head 指针指向.

git branch -d dev 删除库

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

### 冲突解决:

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用git log --graph命令可以看到分支合并图。

git merge --no-ff -m "add cba.txt to dev2 resp"  表示禁用fast-forword

Git分支十分强大，在团队开发中应该充分应用。

合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

git stash  保存工作现场. 

git stash list 查看所有工作区保存列表

git stash apply 恢复 ,但是工作区还在,需要 git stash drop 来删除 工作区

git stash pop 恢复的同时将stash 内容删除 . 

$git branch -d feature-vulcan  删除分支 

如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。

git remote -v 查看远端内容

git pull  从远端获取最新的库内容

git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：

git branch --set-upstream dev origin/dev  设置本地与远端分支的关联

如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name

查看远程库信息，使用git remote -v；

### 多人协作

本地新建的分支如果不推送到远程，对其他人就是不可见的；

从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；

在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；

从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

git tag 设置标签 

git tag v1.0

git tag 查看所有标签 

git show tagname 查看标签内容

git tag -a v0.1 -m "version 0.1 is released" commit id

### 创建查看标签

命令git tag <name>用于新建一个标签，默认为HEAD，也可以指定一个commit id；

git tag -a <tagname> -m "blablabla..."可以指定标签信息；

git tag -s <tagname> -m "blablabla..."可以用PGP签名标签；

命令git tag可以查看所有标签。

git tag -d v0.1 删除标签

git push origin v0.1 推送某个标签至远端

git push origin --tags 一次性推送全部尚未推送到远程的本地标签：

删除远端标签 先从本地删除 标签 

$ git tag -d v0.9
Deleted tag 'v0.9' (was 6224937)

从远程删除。删除命令也是push，但是格式如下：

$ git push origin :refs/tags/v0.9

### 操作标签

命令git push origin <tagname>可以推送一个本地标签；

命令git push origin --tags可以推送全部未推送过的本地标签；

命令git tag -d <tagname>可以删除一个本地标签；

命令git push origin :refs/tags/<tagname>可以删除一个远程标签。
