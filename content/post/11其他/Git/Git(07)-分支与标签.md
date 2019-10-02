---
categories:
- 其他
date: "2019-08-09T22:51:28+08:00"
lastmod: "2019-08-09T22:51:28+08:00"
tags:
- git
title: Git(07)-分支与标签
---

## 1. git分支的本质 #

几乎每一种版本控制系统都以某种形式支持分支。使用分支意味着你可以从开发主线上分离开来,然后在不影响主线的同时继续工作。

> 在很多版本控制系统中,这是个昂贵的过程,常常需要创建一个源代码目录的完整副本,对大型项目来说会花费很长时间。有人把 Git 的分支模型称为“必杀技特性”,而正是因为它,将 Git 从版本控制系统家 族里区分出来。Git 有何特别之处呢?Git 的分支可谓是难以置信的轻量级,它的新建操作 几乎可以在瞬间完成,并且在不同分支间切换起来也差不多一样快。

Git 中的分支，其实本质上仅仅是个指向commit 对象的可变指针（这里的指针在git里实际上就是一个references，对应.git/refs/heads或.git/refs/remotes下面的文件，文件内容是对应commit对象的hash值）。在创建了一个空白仓库，并执行了提交操作之后，git实际上就已经创建了一个分支了，git使用master 作为该分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的master 分支，它在每次提交的时候都会自动向前移动。

而所谓的创建新分支的操作，其实质就是创建了一个新的分支指针，该指针指和原分支指向同一个commit对象。两个分支之间可以来回切换，在不同的分支上进行提交活动，会导致当前分支（指针）向自己的方向向前移动。

Git 如何知道当前在哪个分支上工作呢？git采用一个名为HEAD 的特别指针，用以指向你正在工作中的本地分支的指针。HEAD文件中存放当前分支文件的信息。注意，这与普通的分支指针（引用）是不一样的。实际上，如果把分支看成是指向commit对象的指针，则HEAD就是指向指针的指针。

* 分支指针实际上就是一个commit对象的引用文件，文件内容是对应分支的commit对象的SHA-1值。
* HEAD指针则是一个包含当前所在分支的引用的文件，文件内容是对应的引用文件名。

```
$ cat .git/HEAD 
ref: refs/heads/master
$ cat .git/refs/heads/master
a48f601ff0bea031ee32ec1ba3a55dd243798a34
$ ls -l .git/objects/a4/8f601ff0bea031ee32ec1ba3a55dd243798a34 
-r--r--r--  1 sweeat  wheel  148  7 22 23:23 .git/objects/a4/8f601ff0bea031ee32ec1ba3a55dd243798a34
$ git cat-file -t a48f601ff0bea031ee32ec1ba3a55dd243798a34
commit
$ git cat-file -p a48f601ff0bea031ee32ec1ba3a55dd243798a34
tree ff8f9ee08da4906fd329b25b3ce20b796af87e52
parent cc01f033dd061a68308eeee0467a5399fd984366
author yixy2 <youzhilane@gmail.com> 1563809020 +0800
committer yixy2 <youzhilane@gmail.com> 1563809020 +0800

mytestfile
```

采用HEAD指针的概念，切换分支实际上完成了两个动作：

1. 改变HEAD文件中的值，使其所指向新的分支引用文件。
2. 把工作目录中的已跟踪文件换成新分支所指向的快照内容。

不过在进行分支切换之前，留心你的暂存区或者工作目录里，那些还没有提交的修改，它可能会和你即将检出的分支产生冲突从而阻止Git 为你转换分支。转换分支的时候最好保持一个清洁的工作区域。

## 2. tag的本质 ##

tag对象就像是一个分支引用，但是它不会变化——永远指向同一个commit，仅仅是提供一个更加友好的名字。注意，tag对象是git对象。

Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。轻量级标签（lightweight tag）指向一个commit对象，含附注的标签（annotated tag）则指向一个tag对象，它们都对应.git/refs/tags下的文件。

```
#查看标签列表
$ git tag
#查看具体标签
$ git show v1.3
#建立轻量级标签
$ git tag v1.3-lw
#建立含附注的标签
$ git tag -a v1.4 -m 'my version 1.4'
#建立签署标签
$ git tag -s v1.5 -m 'my signed 1.5 tag'
#验证标签
$ git tag -v [tag-name]
# 默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。
$ git push origin [tagname]
```

> 含附注标签，实际上是一个指向tag对象的引用，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用GNU Privacy Guard (GPG) 来签署或验证。一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

```
$ git tag v1
$ git tag -a v2 -m 'version 2'

#轻量级tag本质上指向一个commit对象
$ cat .git/refs/tags/v1
74f5c663d7dba3c1fe835bab6c776fdb63196063
$ git cat-file -t 74f5c663d7dba3c1fe835bab6c776fdb63196063
commit
$ git cat-file -p 74f5c663d7dba3c1fe835bab6c776fdb63196063
tree 2d68cb4670ffdfdc7e4475a4e9941553e3b55cdc
parent 0af46505d1eded64f9a1db35450df72b9607198d
author yixy2 <yisweeat@outlook.com> 1563838161 +0800
committer yixy2 <yisweeat@outlook.com> 1563838161 +0800

mytest

#含附注的tag本质上指向一个tag对象
$ cat .git/refs/tags/v2
f6a008931c8ec1e3f993e67f7b9ccf67870d3e8a
$ git cat-file -t f6a008931c8ec1e3f993e67f7b9ccf67870d3e8a
tag
$ git cat-file -p f6a008931c8ec1e3f993e67f7b9ccf67870d3e8a
object 74f5c663d7dba3c1fe835bab6c776fdb63196063
type commit
tag v2
tagger yixy2 <yisweeat@outlook.com> 1563839656 +0800

version 2
```