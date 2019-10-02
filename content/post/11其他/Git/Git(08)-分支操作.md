---
categories:
- 其他
date: "2019-11-16T23:54:48+08:00"
lastmod: "2019-11-16T23:54:48+08:00"
tags:
- git
title: Git(08)-分支操作
---

> git中新建分支为什么快?由于Git 中的分支实际上仅是一个包含所指对象校验和（40 个字符长度SHA-1 字串）的文件，所以创建和销毁一个分支就变得非常廉价。说白了，新建一个分支就是向一个文件写入41 个字节（外加一个换行符）那么简单，当然也就很快了。

## 1. 分支的新建及检出 ##

新建分支，实际上就是创建了一个指向当前分支最后一次commit对象的引用文件。

检出某个分支，实际上就是修改HEAD文件的内容，使其内容包含这个分支的引用，并把工作目录中的文件换成这个分支所指向的快照内容。

```
#新建分支
$ git branch [分支名]
#检出分支
$ git checkout [分支名]
#新建分支并检出该分支
$ git checkout -b [分支名]

#查看分支清单
$ git branch
#查看有哪些分支，及各个分支最后一次commit 信息
$ git branch -v
#查看所有分支，包括远程分支
$ git branch -a

#删除分支，-D强制删除
$ git branch -d testing
#git branch -d 删除未合并的分支会导致失败。如果确认可以强制删除：
$ git branch -D testing
```

## 2. 分支合并 ##

git提供分支合并的功能，有两种合并分支的方式：合并和衍合。衍合按照每行改变发生的次序重演发生的改变，而合并是把最终结果合在一起。

* 合并提交（merge commit）：只需检出想要更新的分支，并运行git merge 命令指定来源。Git 会用两个分支的末端和它们的共同祖先进行一次简单的三方合并计算，对三方合并的结果作一新的快照，并自动创建一个指向它的commit。最后，当前分支指向合并后的那个commit。
* 衍合提交（rebase commit）：只需检出想要更新的分支，rebase 命令把当前检出分支里提交的改变在另一个分支里重放一遍。它的原理是回到两个分支（你所在的分支和你想要衍合进去的分支）的共同祖先，提取你所在分支每次提交时产生的差异（diff），把这些差异分别保存到临时文件里，然后从当前分支转换到你需要衍合入的分支，依序施用每一个差异补丁文件。最后将原当前分支指向衍合后的最后一个commit。

```
#merge：将指定分支以merge方式合并到当前分支
$ git merge 源分支
#rebase：
其原理是回到两个分支（当前分支和要衍合的目标分支）的共同祖先，提取当前分支每次提交时产生的差异（diff），把这些差异分别保存到临时文件里，然后检出到需要衍合入的分支，依序施用每一个差异补丁文件。
在衍合的时候，实际上抛弃了一些现存的commit 而创造了一些类似但不同的新commit。
所以，一定要注意，永远不要衍合那些已经推送到公共仓库的更新。
$ git rebase 目标分支
```

注意，merge的时候，若两个分支没有共同祖先，则可以通过`--allow-unrelated-histories`来显式指定合并。

查看合并情况。

```
#查看哪些分支已被并入当前分支
$ git branch --merged
#查看尚未合并入当前分支的工作
$ git branch --no-merged
```

## 3. 处理冲突 ##

有时候合并操作并不会如此顺利。虽然一般情况下git能够进行自动合并，如果你修改了两个待合并分支里同一个文件的同一部分，Git 就无法干净地把两者合到一起（逻辑上说，这种问题只能由人来解决）。Git 会在有冲突的文件里加入标准的冲突解决标记，可以通过它们来手工定位并解决这些冲突。在解决了所有文件里的所有冲突后，运行git add 将把它们标记为已解决（resolved）。因为一旦暂存，就表示冲突已经解决。若所有冲突都已解决，就可以用gitcommit 来完成这次合并提交。

## 4. detached HEAD ##

detached HEAD是指HEAD指向的commit是没有和分支绑定的。这种情况下如果新增commit，由于没有和分支绑定，在分支切换后可能会被GIT丢弃。

```
#checkout到一个没有和分支绑定的commit，进入到detached HEAD状态
$ git checkout d042d5fe736a0395f53f2627a561f24ea2f15c02
Note: checking out 'd042d5fe736a0395f53f2627a561f24ea2f15c02'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at d042d5f mod a
```

## 5. 使用rebase变更历史commit ##

* 在讨论下面的内容之前，需要注意：远程分支上不允许修改历史提交，因为其他开发者可能已经基于这些历史提交进行开发了！！！！！！

使用rebase对commit历史做变更。

```
#最近一次提交的message做变更。注意，最后的commit会被变更，其hash值也会重新生成。
git commit --amend

#使用rebase对多个commit历史做变更。-i指定是交互模式。这时，GIT会让提交者对这些commit进行选择和处理。实际上，整个过程HEAD其实是有进入detached HEAD状态的，只是GIT最终都恢复回来了
git rebase -i 对应父提交
```
