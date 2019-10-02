# 性能分析(5)——IO #

## 1. vmstat ##

分析io性能，最常用的仍然是万能的vmstat。

## 2. iostat——块设备负载性能 ##

显示块设备的实际负载和性能信息。

```
$ iostat -xz 1
Linux 4.10.4-1.el6.elrepo.i686 (host.localdomain)   2018年02月21日     _i686_  (3 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    0.00    0.01    0.00   99.99

Device:         rrqm/s   wrqm/s     r/s     w/s   rsec/s   wsec/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sda               0.00     0.03    0.01    0.07     0.72     3.17    47.89     0.00    6.17    0.67    7.00   3.82   0.03
# Warning! Do not trust this field(svctm) any more. This field will be removed in a future sysstat version.
```

* rrqm/s, wrqm/s：每秒合并读和写操作的次数。如果两个I/O操作发生在相邻的数据块时，它们可以被合并成一个，以提高效率，合并的操作通常是I/O scheduler（也叫elevator）负责的。
* r/s, w/s, rkB/s, wkB/s：分别表示每秒发给磁盘设备的读请求数，每秒发给磁盘设备的写请求数，每秒从磁盘设备读取的KB数，每秒向磁盘设备写入的KB数。可以使用它们表示负载特性。性能问题可能就是由过多的负载造成的。有的系统显示rsec/s和wsec/s，代表每秒读写的扇区数。
* await：平均I/O响应时间，单位为毫秒。包括排队时间和服务时间。如果它大于预期的平均时间，可能是设备已经饱和，也可能是设备存在问题。
* avgrq-sz：每个I/O的平均扇区数。
* avgqu-sz：提交到设备的平均请求数。如果大于1，设备可能已经饱和。
* %util：设备使用率。设备忙于处理请求的百分比。如果大于60%，通常会导致较差的性能（可以在await中看出来），不过也与具体的设备有关。如果接近100%，通常意味着设备已经饱和。
* svctm：已被废弃的指标，没什么意义，svctm=[util/tput]

## iotop ##

类似于top命令，只不过iotop显示每个进程的io情况。

