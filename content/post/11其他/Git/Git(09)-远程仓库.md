---
categories:
- 其他
date: "2019-11-16T23:58:32+08:00"
lastmod: "2019-11-16T23:58:32+08:00"
tags:
- git
title: Git(09)-远程仓库
---

## 1. 克隆（git clone） ##

可以使用git clone命令来备份仓库。git clone 支持采用多种协议表示仓库地址。

* 本地协议：file:///path/repo.git
* http协议：http(s)://host:port/path/repo.git
* ssh协议：user@host:file:///path/repo.git

```
git clone file:///xxx/repo.git

#使用--bare指定克隆不带工作区的仓库。
git clone --bare file:///xxx/.git yyy.git
```

> 其实还有一种Git 协议。这是一个包含在Git 软件包中的特殊守护进程； 它会监听一个提供类似于SSH 服务的特定端口（9418），而无需任何授权。用Git 协议运营仓库，你需要创建git-export-daemon-ok 文件――它是协议进程提供仓库服务的必要条件――但除此之外该服务没有什么安全措施。要么所有人都能克隆Git 仓库，要么谁也不能。这也意味着该协议通常不能用来进行推送。你可以允许推送操作；然而由于没有授权机制，一旦允许该操作，网络上任何一个知道项目URL 的人将都有推送权限。

## 2. 关联远程仓库 ##

远程仓库是指托管在网络上的项目仓库，可供拉取和推送数据，分享各个成员各自的工作进展，远程仓库可能会有好多个。本地仓库可以添加对应的远程仓库配置（关联远程仓库）。

```
#根据url在本地仓库下添加远程仓库配置，该远程仓库配置通过remote-name别名标识。
$ git remote add [remote-name] [远程仓库url]

#修改远程仓库别名
$ git remote rename old new

#废除远程仓库，废除的是本地的远程仓库配置
$ git remote rm new

#查看当前远程分支，若指定 -v for verbose
$ git remote
$ git remote show [remote-name]
```

## 3. 远程分支与跟踪分支 ##

远程分支Remote是从远程仓库上同步下来的，Remote分支不能被check out。Git把他们当作是这些分支在远端服务器上最后状态的一种书签。所以，远程分支并不能直接用于本地开发，需要合并到本地分支，或者使用跟踪分支（跟踪分支也是一种本地分支）才能在本地进行开发提交工作。

* 远程分支（remote branch）：是对从远程仓库同步到本地的分支的索引。它们实际上是一些无法移动的本地分支，只有在进行Git 的网络活动时才会更新。远程分支就像是书签，提醒着你上次连接远程仓库时上面各分支的位置。我们用`remote/远程仓库名/分支名` 这样的形式表示远程分支。
* 跟踪分支(tracking branch)：跟踪分支是一种和远程分支有直接联系的 本地分支。在跟踪分支里输入 git push，Git 会自行推断应该向哪个远程仓库的哪个分支推送数据。反过来，在 这些分支里运行 git pull 会获取所有远程索引，并把它们的数据都合并到本地分支中来。

```
#创建自定义的跟踪分支
git checkout -b [分支名] [remote-name]/[远程分支名]

#git1.6.2以上版本，还支持通过--track简化方式直接创建跟踪分支，这种情况下跟踪分支命名格式默认取自远程分支
git checkout --track [remote-name]/[远程分支名]

#--track的例子
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch refs/remotes/origin/serverfix. Switched to a new branch "serverfix"

#删除远程分支
git push origin --delete <BranchName>

#查看跟踪分支与远程分支关系
git branch -avv
```

如果是克隆了一个仓库，会自动添加一个默认名为origin的远程仓库。实际上，默认情况下git clone 命令本质上就是自动创建了本地的master 分支用于跟踪远程仓库中的master 分支（假设远程仓库确实有master 分支）。这正是下文提到的git push 和git pull 一开始就能正常工作的原因。

## 4. git fetch 与 git pull ##

* git fetch：该命令会到指定的远程仓库中拉取所有你本地仓库中还没有的数据。有一点很重要，需要记住，fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支，只有当你确实准备好了，才能手工合并。
* git push：要想和其他人分享某个本地分支，你需要使用git push命令把它推送到一个你拥有写权限的远程仓库。注意，你的本地分支不会被自动同步到你引入的远程分支中，除非你明确执行推送操作。另外，只有在所克隆的服务器上有写权限，或者同一时刻没有其他人在推数据，push命令才会如期完成任务。如果在你推数据前，已经有其他人推送了若干更新，那你的推送操作就会被驳回。你必须先把他们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。

```
#从远程仓库拉取所有本地仓库中还没有的数据
git fetch [remote-name]
#推送同步到远程仓库
git push [remote-name]
#pull分两步，一是fetch，二是merge到本地
git pull

#向远程仓库推送分支更新
$ git push [remote-name] [本地分支]:[远程分支]
#若本地分支名与远程分支名相同，可以写成如下形式
$ git push [remote-name] [branch-name]

#另外，在跟踪分支里输入git push，Git 会自行推断应该向哪个服务器的哪个分支推送数据。
$ git push
```

注意，如果不再需要某个远程分支了，比如搞定了某个特性并把它合并进了远程的 master 分支(或任何其他存放 稳定代码的地方)，可以用这个非常无厘头的语法来删除它:git push [远程名] :[分支名]。如果想在服务器上删 除 serverfix 分支，运行下面的命令，服务器上的分支没了。（等于是在说“在这里提取空白然后把它变成[远程分支]）

```
$ git push origin :serverfix
To git@github.com:schacon/simplegit.git
- [deleted] serverfix

#删除分支
git branch --d fix_burncpu
# for --d --f 
git branch --D fix_burncpu

#删除本地的远程分支
git branch --delete --remote origin/fix_burncpu
```

## 5. 多人合作禁忌：永远不要rebase那些已经推送到远程仓库的更新 ##

注意，在公开分支上进行rebase操作会带来问题。永远不要rebase那些已经推送到公共仓库的更新。因为在rebase的时候，实际上抛弃了一些现存的commit 而创造了一些类似但不同的新commit。rebase会改变这些commit的SHA-1 校验值，这样Git 会把它们当作新的commit，然而这时候在你的提交历史早就有了这部分的内容。如果你把commit 推送到某处然后其他人下载并在其基础上工作，然后你用git rebase 重写了这些commit 再推送一次，你的合作者就不得不重新合并他们的工作，这样当你再次从他们那里获取内容的时候事情就会变得一团糟。如果把rebase当成一种在推送之前清理提交历史的手段，而且仅仅rebase那些永远不会公开的commit，那就不会有任何问题。如果rebase那些已经公开的commit，而与此同时其他人已经用这些commit 进行了后续的开发工作，那你有得麻烦了。

## 6. 多人合作禁忌：永远不要push -f方式强制向远处公共仓库推送更新 ##

git push -f 即使是 non-fast-forward也能push，该命令需要禁止，否则可能造成远程仓库的commit丢失。

注意，github可以配置禁用git push -f 。

可以使用fork的机制，解决这些问题。

## 7. 技巧 ##

1. 不同人修改了不同文件如何处理？           git fetch -> merge/rebase（自动归并） -> git push
2. 不同人修改了同文件的不同区域如何处理？    git fetch -> merge/rebase（自动归并） -> git push
3. 不同人修改了同文件的同一区域如何处理？    git fetch -> merge/rebase（手工归并） -> git push
4. 同时变更了文件名和文件内容如何处理？      git fetch -> merge/rebase（自动归并） -> git push
5. 把同一文件改成了不同的文件名如何处理？    git fetch -> merge/rebase（git add + git rm 一项一项处理;最后git commit） -> git push

可以使用rerere记录备案，解决rebade重复解决冲突的问题

```
git config --global rerere.enabled true
git merge xxx
解决冲突
git add 
git commit 
git reset --hard HEAD~1
```
