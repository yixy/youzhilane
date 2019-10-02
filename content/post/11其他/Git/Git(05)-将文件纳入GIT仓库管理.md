---
categories:
- 其他
date: "2019-11-16T23:41:13+08:00"
lastmod: "2019-11-16T23:41:13+08:00"
tags:
- git
title: Git(05)-将文件纳入GIT仓库管理
---

## 1. unstaged文件移入暂存区（stage）并提交（commit） ##

通过在commit前增加一个暂存区的概念，实现可以用多个指令精确的挑选出需要提交的所有修改，然后再一次性的（原子性的）提交到版本库。

![git暂存区](/youzhilane/img/Git-暂存区.jpg)

工作目录中unstaged的文件移入暂存区。

```
#将文件移入暂存区，对应两种情况：
#1、未跟踪文件，变为已跟踪文件，移入暂存区
#2、已跟踪文件，发生修改后未被暂存，移入暂存区
$ git add README.txt

# 针对第2种情况，可以使用如下命令将已跟踪文件的所有修改文件移入暂存区
git add -u
```

暂存区文件提交至仓库。

```
git commit -m 'this is commit message'
```

其他命令。

```
#检查工作目录下文件状态
$ git status

#取消unstaged状态的文件修改，注意，因为没有暂存提交，此时取消修改无法找回
$ git checkout -- README.txt

#取消将文件移入暂存区的操作（等价于git reset HEAD README.txt）
$ git reset HEAD -- README.txt

#撤销最近几次的commit。暂存区的内容也同步丢弃。
git reset --hard <commit hash>

#此命令将使用当前的暂存区域快照提交。如果上一次提交完没有作任何改动，直接运行此命令的话，相当于将本次提交与上一次提交合并，生成一份快照。
$ git commit --amend

#修改author
git commit --amend --author="NewAuthor <NewEmail@address.com>"

#跳过暂存区提交。自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过git add 步骤
$ git commit -a -m 'added new README'
```

## 2. 删除与移动文件 ##

删除文件。

```
#删除文件，并从仓库中移除（在缓存区中待提交）
$ git rm README.txt
#仓库中移除文件（在缓存区中待提交），但不删除文件（变为未跟踪）
$ git rm --cached README.txt
```

删除与移动文件。

```
#删除文件（在缓存区中待提交）
$ git rm README.txt
#移动文件（在缓存区中待提交）
$ git mv file_from file_to
#git mv等价于以下命令：
$ mv README.txt README
$ git rm README.txt
$ git add README
```

## 3. 查看提交历史 ##

```
#查看提交历史
$ git log
$ git log --online
$ git log --all
#最近4次提交记录
$ git log -n4
$ git log --graph
```

## 4. 差异对比 ##

```
#查看尚未暂存的文件修改了哪些部分（工作空间与暂存区的差异）
$ git diff
#查看已经暂存起来的文件和上次提交时的快照之间的差异（暂存区与仓库的差异）
$ git diff --cached

#查看当前提交和祖父提交的差异
git diff HEAD HEAD~1~1
# 比较两个commit(或分支)之间的差异
git diff commit1 commit2 -- <file>
git diff branch1 branch2 -- <file>
```

## 5. stash ##

```
#将暂存区内容stash
git stash
#查看stash内容
git stash list 

#从stash中恢复，stash中仍保留
git stash apply
#从stash中恢复，stash中不保留
git stash pop
```

## 6. 修改历史提交 ##

可以使用rebase对commit历史做变更，-i指定需要修改的提交的父提交。

```
git rebase -i 对应父提交
```

注意，有一个原则：

* 共享分支上不允许修改历史提交，因为其他开发者可能已经基于这些历史提交进行开发了。

所以，若已提交至原程分支，最好不要用rebase。
