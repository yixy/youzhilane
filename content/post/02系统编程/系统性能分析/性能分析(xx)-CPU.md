
# 性能分析(3)——CPU #

## 1. top ##

一般情况下，我们可以使用top命令分析CPU运行情况，这就足够了。mpstat和pidstat等提供的功能top命令基本也都提供了。

## 2. /proc/stat文件——查看CPU时间 ##

详细的CPU信息可查看/proc/stat文件，包含CPU时间等信息。

```
#查看CPU时间
cat /proc/stat
```

## 3. mpstat——查看单核CPU ##

Sysstat的工具集包含了sar、iostat、pidstat、mpstat、sadf、sar、sadc等工具。其中，mpstat可以用来检查各CPU的负载是否均衡。比如，如果一个CPU很热，可能是单线程应用造成的。

```
#-P指定CPU编号，1s打印一次。
mpstat -P ALL 1
```

## 4. pidstat——进程的CPU使用情况 ##

pidstat按进程打印CPU的使用情况。

```
#-p指定pid，1s打印一次。
$ pidstat -p ALL 1
```

## 相关链接 ##

《性能之巅》，Brandan Gregg，第6章，CPU

ps -eo pid,lstart,etime,cmd