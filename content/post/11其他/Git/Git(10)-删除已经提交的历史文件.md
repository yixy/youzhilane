---
categories:
- 其他
date: "2019-11-17T00:22:22+08:00"
lastmod: "2019-11-17T00:22:22+08:00"
tags:
- git
title: Git(10)-删除已经提交的历史文件
---

在使用Git时，即使使用`git rm`删除了某个文件，但仓库中实际上还是保存了这个文件。如果想要删除已经提交了的文件，是一件很麻烦的事情，因为它存在于历史提交中。

***针对这个问题，好的解决方案是认真对待每一次提交，避免类似的事情发生。***

不推荐的解决方案是使用 filter-branch 命令重写从某个commit开始的所有 commit，将文件从 Git 历史中完全移除。但是实际上操作起来比较复杂，并且仓库有很多历史提交时执行效率很慢。同时，这样的操作实际上修改了仓库历史提交，如无必要，还是尽量不要这样做。

```
git filter-branch --index-filter 'git rm --cached --ignore-unmatch db.properties*' --prune-empty --tag-name-filter cat -- --all
git push origin master --force 
rm -rf .git/refs/original/  
git reflog expire --expire=now --all  
git gc --prune=now  
git gc --aggressive --prune=now
```

注意，git专门有`git prune`命令，用于删除冗余的git对象文件。Prune all unreachable objects（unreachable objects 指的是.git\objects中没有被使用的hash文件） from the object database 。
