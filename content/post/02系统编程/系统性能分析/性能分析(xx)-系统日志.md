
# 性能分析(7)——系统日志 #

一般，Linux所有服务的登录信息文件或错误信息文件（LOG FILES)都在/var/log下，此外，一些数据库如MySQL则在/var/lib下，还有，用户未读的邮件的默认存放地点为/var/spool/mail

* 系统的引导日志：/var/log/boot.log
* 核心启动日志：/var/log/dmesg
* 系统报错日志：/var/log/messages
* 邮件系统日志：/var/log/maillog
* FTP系统日志：/var/log/xferlog
* 安全信息和系统登录与网络连接的信息：/var/log/secure
* 记录所有的登录和登出信息：/var/log/wtmp（二进制文件，使用命令who -u /var/log/wtmp 查看信息）
* 记录当前登录用户：/var/run/utmp
* 记录每个用户的最后登录信息：/var/log/lastlog
* 记录错误的登录尝试：/var/log/btmp
* News日志：/var/log/spooler
* RPM软件包：/var/log/rpmpkgs
* XFree86日志：/var/log/XFree86.0.log
* cron(定制任务日志)日志：/var/log/cron
* 安全信息和系统登录与网络连接的信息：/var/log/secure
* 需要身份确认的操作：/var/log/auth.log

## 1. message ##

messages 日志是核心系统日志文件。它包含了系统启动时的引导消息，以及系统运行时的其他状态消息。IO错误、网络错误和其他系统错误都会记录到这个文件中。其他信息，比如某个人的身份切换为 root，也在这里列出。如果服务正在运行，比如DHCP 服务器，您可以在 messages 文件中观察它的活动。通常，/var/log/messages是您在做故障诊断时首先要查看的文件。

## 2. syslog ##

/var/log/syslog，默认RedHat Linux不生成该日志文件，但可以配置/etc/syslog.conf让系统生成该日志文件。它和/etc/log/messages日志文件不同，它只记录警告信息，常常是系统出问题的信息，所以更应该关注该文件。要让系统生成该日志文件，在/etc/syslog.conf文件中加上： *.warning /var/log/syslog该日志文件能记录当用户登录时login记录下的错误口令、Sendmail的问题、su命令执行失败等信息。

## 3. dmesg ##

dmesg命令被用于检查和控制内核的环形缓冲区。kernel会将开机信息存储在ring buffer中。您若是开机时来不及查看信息，可利用dmesg来查看。开机信息保存在/var/log/dmesg文件里。

```
dmesg
```