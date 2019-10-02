---
categories:
- 其他
date: "2019-11-16T22:25:57+08:00"
lastmod: "2019-11-16T22:25:57+08:00"
tags:
- git
title: Git(03)-新建仓库
---

## 1. 将已有项目代码纳入git管理 ##

```
$ cd 项目代码目录
$ git init
```

## 2. 创建空白仓库 ##

可以使用如下命令新建一个目录，并在该目录中创建一个空白的仓库。仓库默认命名为.git。

```
$ git init mygit
Initialized empty Git repository in /data/mygit/.git/
$ cd mygit
$ ls
.git
$ cd .git
$ ls 
HEAD        config      description hooks       info        objects     refs
```

也可以直接创建一个不带工作空间的仓库。不带工作空间的仓库习惯上命名为xxx.git。

```
$ git init --bare mygit.git
$ cd mygit.git/
$ ls
HEAD        config      description hooks       info        objects     refs
```
