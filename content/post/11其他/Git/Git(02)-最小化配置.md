---
categories:
- 其他
date: "2019-11-16T21:58:52+08:00"
lastmod: "2019-11-16T21:58:52+08:00"
tags:
- git
title: Git(02)-最小化配置
---

## 1. git config ##

Git 提供了一个叫做git config 的工具（实际是git-config 命令，只不过可以通过git 加一个名字来呼叫此命令），专门用来配置或读取相应的工作环境变量。而正是由这些环境变量，决定了Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

 * /etc/gitconfig文件：系统中对所有用户都普遍适用的配置。若使用git config 时，用--system 选项，读写的就是这个文件。
 * ~/.gitconfig文件：用户目录下的配置文件只适用于该用户。若使用git config 时，用--global 选项，读写的就是这个文件。
 * .git/config文件：当前仓库的git 目录中的配置文件，这里的配置仅仅针对当前仓库有效，用--local 选项。不指定时默认是--local

注意，上面每一个级别的配置都会覆盖上层的相同配置，例如.git/config 里的配置会覆盖/etc/gitconfig 中的同名变量。

## 2. 常用配置 ##

最小化配置。

```
$ git config --global user.name "yixy"
$ git config --global user.email youzhilane01@gmail.com
```

查看配置。

```
$ git config --list --global
```

其他配置。

```
$ git config --global core.editor emacs
$ git config --global merge.tool vimdiff
```

清除配置。

```
$ git config --unset --local user.name
$ git config --unset --global user.name
$ git config --unset --system user.name
```