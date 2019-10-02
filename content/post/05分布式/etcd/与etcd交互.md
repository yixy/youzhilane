---
#author: "yixy"
title: "与etcd交互"
date: 2019-09-28T09:25:16+08:00
lastmod: 2019-09-28T09:25:16+08:00
tags: [
    "分布式","注册中心","Golang"
]
categories: [
    "分布式"
]
# you can close something for this content if you open it in config.toml.
#comment: false
#mathjax: false
#contentCopyright: false
---

# etcd交互场景 #

etcd本身是采用Golang实现的，支持多种语言客户端对接，同时也支持HTTP的API。

etcd官方文档（Interacting with etcd
）中列举了如下使用场景。

* Find versions：etcdctl使用的APIVersion配置
* Write a key
* Read keys
* Read past version of keys
* Read keys which are greater than or equal to the byte value of the specified key
* Delete keys
* Watch key changes
* Watch historical changes of keys
* Watch progress
* Compacted revisions
* Grant leases：建立租约
* Revoke leases
* Keep leases alive：续租
* Get lease information

其他场景。

* txn事务 
* 并发控制命令lock：注意，只有当正常退出且释放锁后，lock命令的退出码是0，否则这个锁会一直被占用直到过期（默认60秒）
* 权限命令：user 可以为etcd创建多个用户并设置密码；role 可以为etcd创建多个角色并设置权限；auth 开启/关闭权限控制。

## 相关链接 ##

etcd v3.4.0官方文档

https://etcd.io/docs/v3.4.0/dev-guide/interacting_v3/