---
categories:
- 云计算
date: "2019-09-30T22:15:58+08:00"
lastmod: "2019-09-30T22:20:38+08:00"
tags:
- 云计算
- Golang
title: Go语言测试(1)-单元测试与基准测试
---

## 1. 使用testing包进行单元测试与基准测试 ##

Golang本身提供了对单元测试和基准测试的支持，引入testing包后，遵守如下约定使用即可。

单元测试：

* 文件名必须以xx_test.go命名
* 方法必须是TestXxx开头
* 方法入参数必须是* testing.T类型，且方法不能有返回值

基准测试：

* 文件名必须以xx_test.go命名
* 方法必须是BenchmarkXxx开头
* 方法入参数必须是* testing.B类型，且方法不能有返回值
* b.ResetTimer是重置计时器，这样可以避免for循环之前的初始化代码的干扰
* b.N是基准测试框架提供的，表示循环的次数

***注意，使用命令行进行测试应该指定目录(包导入路径)，若不指定默认是当前目录。也可以通过run/bench参数指定要执行的单元/基准测试案例。如果指定具体的xx_test.go文件来进行测试可能会报编译错误（ command-line-arguments [build failed]）。***

一个简单的例子。注意，单元测试中的`t.Error`方法并不中断程序的执行。

```
package main
import (
    "testing"
    "time"
)
func myPrints(){
    for i:=0;i<1000;i++{
        fmt.Println(i)
    }
}
func TestPrints(t *testing.T) {
    myPrints()
    t.Log("ok")
}

func BenchmarkPrints(b *testing.B){
    num:=10
    b.ResetTimer()
    for i:=0;i<b.N;i++{
        b.StopTimer()
        //与性能测试无关的代码
        b.StartTimer()
        fmyPrints()
    }    
}
```

单元测试可以通过`-run`参数指定具体的单元测试案例，也可以通过`--cover`参数指定打印单元测试覆盖率。

```
//单元测试 指定路径
go test -v .

//指定特定的测试案例
go test -v . -run=TestXxx

//统计代码覆盖率（非xx_test.go文件），可以使用cover参数。如果需要生成文件，可以指定-html参数
go test -v --cover .
//测试覆盖率是用于通过执行某包的测试用例来确认描述其的代码在测试用例中被执行的程度的术语。
```

基准测试可以通过`-bench`命令指定具体的基准测试案例，同时可以通过`-run`指定一个不存在的单元测试案例以避免执行其他单元测试，对基准测试造成影响。

`go test`命令会运行目标代码 b.N 次，并且在执行期间，会自动调整 b.N 直到基准测试函数持续足够长的时间。如果想让测试运行的时间更长，可以通过`-benchtime`参数指定。`-benchmem`可以指定打印每次操作分配内存的次数，以及每次操作分配的字节数。

* `StartTimer()`负责启动计时并初始化内存相关计数，测试执行时会自动调用，一般不需要用户启动。
* `StopTimer()`负责停止计时，并累加相应的统计值。

需要注意，`StopTimer()`并不一定是测试结束，一个测试中有可能有多个统计阶段，所以其统计值是累加的。特别需要注意，`StartTimer()`负责启动计时，并记录当前内存分配情况，不管是否有`-benchmem`参数，内存都会被统计，参数只决定是否要在结果中输出。

```
//当前目录下的基准测试（指定跑一个不存在的单元测试none）
go test -v -bench=. -run=none
//BenchmarkPrints表示运行时对应的GOMAXPROCS的值，后面依次是测试执行的次数（与基准测试代码里for循环的b.N相关，b.N是基准测试框架提供的，表示循环的次数，因为需要反复调用测试的代码，才可以评估性能），以及每次需要花费的时间。
BenchmarkPrints-4       2000000000               0.00 ns/op
```

## 2. go test命令 ##

go test命令的常见用法如下。

```
go test [-c] [-i] [build flags] [packages] [flags for test binary]

-c : 编译go test成为可执行的二进制文件，但是不运行测试。

-i : 安装测试包依赖的package，但是不运行测试。

关于build flags，调用go help build可以查阅，这些是编译运行过程中需要使用到的参数，一般设置为空

关于packages，调用go help packages可以查阅，这些是关于包的管理，一般设置为空

关于flags for test binary，调用go help testflag可以查阅，这些是go test过程中经常使用到的参数

-test.v : 是否输出全部的单元测试用例（不管成功或者失败），默认没有加上，所以只输出失败的单元测试用例。

-test.run pattern: 只跑哪些单元测试用例

-test.bench patten: 只跑那些性能测试用例

-test.benchmem : 是否在性能测试的时候输出内存情况

-test.benchtime t : 性能测试运行的时间，默认是1s

-test.cpuprofile cpu.out : 是否输出cpu性能分析文件

-test.memprofile mem.out : 是否输出内存性能分析文件

-test.blockprofile block.out : 是否输出内部goroutine阻塞的性能分析文件

-test.memprofilerate n : 内存性能分析的时候有一个分配了多少的时候才打点记录的问题。这个参数就是设置打点的内存分配间隔，也就是profile中一个sample代表的内存大小。默认是设置为512 * 1024的。如果你将它设置为1，则每分配一个内存块就会在profile中有个打点，那么生成的profile的sample就会非常多。如果你设置为0，那就是不做打点了。

你可以通过设置memprofilerate=1和GOGC=off来关闭内存回收，并且对每个内存块的分配进行观察。

-test.blockprofilerate n: 基本同上，控制的是goroutine阻塞时候打点的纳秒数。默认不设置就相当于-test.blockprofilerate=1，每一纳秒都打点记录一下

-test.parallel n : 性能测试的程序并行cpu数，默认等于GOMAXPROCS。

-test.timeout t : 如果测试用例运行时间超过t，则抛出panic

-test.cpu 1,2,4 : 程序运行在哪些CPU上面，使用二进制的1所在位代表，和nginx的nginx_worker_cpu_affinity是一个道理

-test.short : 将那些运行时间较长的测试用例运行时间缩短
```
