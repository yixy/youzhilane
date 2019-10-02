
# 性能分析(6)——网络 #

## 1. netstat——查询网络连接、路由表等信息 ##

netstat - Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships

```
#-at指明所有TCP
netstat -at | grep TIME_WAIT | wc -l
#-au指明所有UDP
netstat -au
```

## 2. 连接数的限制——文件描述符 ##

>参考《Unix(4)——理解进程》

一个进程可以拥有的文件描述符数量取决于具体的系统配置（软限制，任何进程可以修改自身的软限制；硬限制：只有超级用户或具有超级用户权限的进程可以修改硬限制）。

查看用户进程文件描述符最大限制。

```
#Linux
#查询文件描述符硬限制
-bash-4.1$ ulimit -Hn
20000
#查询文件描述符软限制
-bash-4.1$ ulimit -n
10000
```

调整用户进程文件描述符最大限制。

```
#Linux
vi /etc/security/limits.conf
#调整文件描述符硬限制
* hard nofile 10000
#调整文件描述符软限制
* soft nofile 20000
```

限制所有用户打开文件描述符的总和，可以通过修改内核参数来更改该限制：

```
#Linux
sysctl -w fs.file-max=102400
```

使用sysctl命令更改是临时的，如果想永久更改需要在/etc/sysctl.conf添加配置。

```
#Linux
vi /etc/sysctl.conf
fs.file-max=102400

sysctl -p
```

保存退出后使用sysctl -p 命令使其生效。有时，这样修改完成后，重启系统，使用ipcs -l后发现，修改的内核参数仍然是默认值，并没有生效。这可能是因为，在系统启动时，加载内核参数后，启动级别较低的服务又重新修改了内核参数。一个较简单的解决方案是在rc.local里，加上sysctl -p，重新加载内核参数。该文件是在系统启动时，最后执行的脚本。

```
#FreeBSD
vi /etc/sysctl.conf
kern.maxfiles=65535

#AIX
vi /etc/security/limits
root:
        fsize = -1
        nofiles = 8192

#OSX
#增加max open files。先查看一下:
$ sysctl -a | grep files
kern.maxfiles = 12288
kern.maxfilesperproc = 10240
$ sudo sysctl -w kern.maxfiles=65535
$ sudo sysctl -w kern.maxfilesperproc=65535
#增加 max sockets
$ sysctl -a | grep somax
kern.ipc.somaxconn: 128
$ sudo sysctl -w kern.ipc.somaxconn=2048
```

## 3. sar——网络流量统计 ##

sar命令是Linux下系统运行状态统计工具，它将指定的操作系统状态计数器显示到标准输出设备。sar工具将对系统当前的状态进行取样，然后通过计算数据和比例来表达系统的当前运行状态。它的特点是可以连续对系统取样，获得大量的取样数据。取样数据和分析的结果都可以存入文件，使用它时消耗的系统资源很小。

```
#报告网络统计信息。检查网络接口的吞吐量，以rxkB/s和txkB/s为手段测量负载。
$ sar -n DEV 1
Linux 4.10.4-1.el6.elrepo.i686 (host.localdomain)   02/21/2018  _i686_  (3 CPU)

05:59:13 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
05:59:14 AM      eth0      3.00      1.00      0.19      0.18      0.00      0.00      0.00
05:59:14 AM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00

05:59:14 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s
05:59:15 AM      eth0      1.00      1.00      0.06      0.06      0.00      0.00      0.00
05:59:15 AM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00
```

* IFACE：Name of the network interface for which statistics are reported.
* rxpck/s：Total number of packets received per second.
* txpck/s：Total number of packets transmitted per second.
* rxkB/s：Total number of kilobytes received per second.
* txkB/s：Total number of kilobytes transmitted per second.
* rxcmp/s：Number of compressed packets received per second (for cslip etc.).
* txcmp/s：Number of compressed packets transmitted per second.
* rxmcst/s：Number of multicast packets received per second.

```
#报告网络统计信息。查询关键TCP指标。TCP关键字查询TCP流量报告，ETCP查询TCP网络错误报告。
$ sar -n TCP,ETCP 1
Linux 4.10.4-1.el6.elrepo.i686 (host.localdomain)   02/21/2018  _i686_  (3 CPU)

06:09:22 AM  active/s passive/s    iseg/s    oseg/s
06:09:23 AM      0.00      0.00      2.02      0.00

06:09:22 AM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
06:09:23 AM      0.00      0.00      0.00      0.00      0.00
```

* active/s：每秒从CLOSED状态发起TCP连接（转换为SYN-SENT）的次数。
* passive/s：每秒从LISTEN状态接收TCP连接（转换为SYN-RCVD）的次数。
* iseg/s：当前已建立的各连接中，每秒收到的segment总数，包括错误的接收。
* oseg/s：当前已建立的各连接中，每秒发送的segment数。
* atmptf/s：每秒从SYN-SENT或SYN-RCVD到CLOSED状态，从SYN-RCVD到LISTEN状态的TCP连接数。
* estres/s：每秒从ESTABLISHED或CLOSE-WAIT到CLOSED状态的TCP连接数。
* retrans/s：每秒TCP重传数。
* isegerr/s：每秒接收到的错误segment数。
* orsts/s：每秒发送TCP segment数，包括RST包。

active和passive连接数通常用于粗略地测量服务器负载。方便起见，可以把active看作向外的连接，把passive看作向内的连接；不过也有不严格之处，比如考虑从localhost到localhost的连接。重传数是网络或服务器问题的一个信号：可能是网络不可靠；也可能是服务器过载和丢包。

> RST表示复位，用来异常的关闭连接，在TCP的设计中它是不可或缺的。就像上面说的一样，发送RST包关闭连接时，不必等缓冲区的包都发出去（不像上面的FIN包），直接就丢弃缓存区的包发送RST包。而接收端收到RST包后，也不必发送ACK包来确认。

## 4. iperf ##

Iperf 是一个网络性能测试工具，可以测试最大TCP和UDP带宽性能。

```
#启动服务端
iperf3 -s
#启动客户端
iperf3 -c 192.168.0.111 -b 100M -t 60 -i 10
```

> 可以配合speedtest工具使用。