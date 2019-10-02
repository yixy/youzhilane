---
categories:
- 其他
date: "2019-11-16T23:42:59+08:00"
lastmod: "2019-11-16T23:42:59+08:00"
tags:
- git
title: Git(06)-探秘.git目录
---

从根本上来讲Git 是一套内容寻址(content-addressable) 文件系统。从内部来看，Git 是简单的key-value 数据存储。它允许插入任意类型的内容，并会返回一个键值，通过该键值可以在任何时候再取出该内容。

几乎所有Git 存储和操作的内容都位于.git目录下。如果你要备份或复制一个库，基本上将这一目录拷贝至其他地方就可以了。

## 1. git对象 ##

Git 存储数据内容的方式是为每份内容生成一个使用 zlib 压缩保存的文件，我们称之为git对象。该文件包含内容本身和一个头信息，头信息中包含对象类型及内容长度等信息。git会取得文件内容与头信息的SHA-1 校验和，在.git/objects目录下创建以该校验和前两个字符为名称的子目录，并以(校验和) 剩下38 个字符作为文件名并保存至子目录下。

虽然git对象分为几种类型，但是所有的Git 对象都以上面提到的这种方式存储，惟一的区别是文件头中的git对象类型不同，另外，文件内容中不同类型可能会有固定的格式(例如commit 和tree对象)。

![commit对象](/youzhilane/img/Git-commit对象.jpg)

* blob对象：移入暂存区的文件，git就会为之创建对象（blob对象）。
* tree对象：当使用git commit 时，git会创建tree对象并保存到git仓库中，这些tree对象可以看成是一棵树，对应该次提交时的文件的目录结构信息。注意，这个tree对象可以看成是对当前工作区已纳入仓库管理的文件的快照（包括本期有变动的和未变动的），可以根据这个树的根节点去检索目录和文件。树中实际存储的是某个目录下一系列文件的文件名，以及对应的git对象的类型（包括blob对象和tree对象)、SHA-1值、权限模式(mode)等信息。
* commit对象：在tree对象保存之后，Git 会创建一个commit对象，用于记录仓库中某次提交的快照。它实际存储的是这次提交对应的tree对象树的根节点的SHA-1值，并且还包含了提交者、提交时间、提交注释，提交对象还可能包含一定数量（也可能没有）指向该commit对象直接祖先的指针（即上次commit对象的SHA-1值）。第一次提交是没有直接祖先的，普通提交有一个祖先，由两个或多个分支合并产生的提交则有多个祖先。
* tag对象：用于指向一个 commit。Tag 对象包含一个标签名,一组数据,一个消息和一个commit对象的SHA-1值。tag就像是一个分支引用，但是它不会变化——永远指向同一个commit，仅仅是提供一个更加友好的名字。

运行　git add 命令时，Git 首先保存修改了的文件的blob，更新索引；运行git commit 命令时，先创建tree 对象，最后创建commit 对象，这些commit 对象指向了顶层tree 对象以及先前的commit 对象。

Git 往磁盘保存git对象时默认使用的格式叫松散对象 (loose object) 格式。Git 时不时地将这些对象打包至一个叫 packfile 的二进制文件以节省空间 并提高效率。当仓库中有太多的松散对象,或是手工调用 git gc 命令,或推送至远程服 务器时,Git 都会这样做。生成packfile 包的同时会生成一个索引，索引文件包含了 packfile 的偏移信息,这样就可以快速定位任意一 个指定对象。Git 打包对象时,会查找命名及尺寸相近的文件,并只保存文 件不同版本之间的差异内容。

```
#查看git对象类型
git cat-file -t 对象hash值
#查看hash对应的git对象
git cat-file -p 对象hash值
#查看所有分支详细信息
git branch -av
```

## 2. .git目录内容 ##

核心内容如下：

* objects：保存文件系统的实际数据内容，该目录存储所有git文件系统的数据内容（即各个类型的git对象）。注意，对每个git对象，会在.git/objects目录下会创建以该校验和前两个字符为名称的子目录，并以(校验和) 剩下38 个字符作为文件名并保存至子目录下。
* refs：该目录存储指向commit提交对象或tag标签的指针(hash值）。分为.git/refs/heads（存放本地分支指针，即该分支最后一个commit的hash）、.git/refs/remotes（存放远程分支指针，即该分支最后一个commit的hash）、.git/refs/tags（含附注的标签指针指向tag对象；轻量级标签指针指向commit对象）等子目录，分别存放分支的最后一个提交对象和tag对应的提交对象的hash值。
* HEAD：文本文件，其内容是refs下的文件名。该文件一般指向当前分支的最后一个提交。通过`git checkout commit对象`命令可以转换为HEAD detached状态，即将HEAD指向某个没有关联分支的提交，这种状态下该提交的内容很容易被git丢弃。
* index：该文件则保存了暂存区域信息。暂存操作会对每一个已跟踪文件计算校验和（SHA-1 哈希字串），然后对当前版本的有修改的已跟踪文件生成新的blob对象，并将其校验和加入暂存区域。注意，所谓的暂存区域只不过是个简单的文件，一般都放在.git 目录中。有时候人们会把这个文件叫做索引文件，不过标准说法还是叫暂存区域。

其余文件及目录的简单描述如下：

* branches：新版本的Git 不再使用branches 目录。
* description：该文件仅供GitWeb 程序使用，所以不用关心该内容。
* config：该文件包含了项目特有的配置选项。
* info：该目录保存了一份不希望在.gitignore 文件中管理的忽略模式(ignored patterns) 的全局可执行文件。
* hooks：该目录包含了客户端或服务端钩子脚本。

```
$ cd .git
$ ls -al
total 40
drwxr-xr-x  12 sweeat  wheel  384  7 21 18:32 .
drwxr-xr-x   3 sweeat  wheel   96  7 21 18:32 ..
-rw-r--r--   1 sweeat  wheel    5  7 21 18:32 COMMIT_EDITMSG    //
-rw-r--r--   1 sweeat  wheel   23  7 21 07:48 HEAD              //当前分支索引
-rw-r--r--   1 sweeat  wheel  137  7 21 07:48 config            //local仓库配置
-rw-r--r--   1 sweeat  wheel   73  7 21 07:48 description       //
drwxr-xr-x  13 sweeat  wheel  416  7 21 07:48 hooks             //
-rw-r--r--   1 sweeat  wheel   65  7 21 18:32 index             //
drwxr-xr-x   3 sweeat  wheel   96  7 21 07:48 info              //
drwxr-xr-x   4 sweeat  wheel  128  7 21 18:24 logs              //
drwxr-xr-x  14 sweeat  wheel  448  7 21 18:32 objects           //
drwxr-xr-x   4 sweeat  wheel  128  7 21 07:48 refs              //分支、tag索引
```