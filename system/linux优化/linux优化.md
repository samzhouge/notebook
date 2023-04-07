# Linux性能优化实战-极客时间
## [Linux性能优化实战](https://time.geekbang.org/column/intro/100020901)

# CPU 性能篇 (13讲)
## 02基础篇：到底应该怎么理解“平均负载”？
* 平均负载是指单位时间内，系统处于可运行状态和不可中断状态的平均进程数，也就是平均活跃进程数，它和 CPU 使用率并没有直接关系。
  * 可运行状态的进程 R（Running 或 Runnable）
  * 不可中断状态的进程 D （Uninterruptible Sleep，也称为 Disk Sleep）
    * 不可中断状态实际上是系统对进程和硬件设备的一种保护机制。
* cpu信息 lscpu
* 平均负载多高时候需要关注：一般当平均负载高于 CPU 数量 70% 的时候
  * 但 70% 这个数字并不是绝对的，最推荐的方法，还是把系统的平均负载监控起来，然后根据更多的历史数据，判断负载的变化趋势。
  * 当发现负载有明显升高趋势时，比如说负载翻倍了，你再去做分析和调查。

### 负载 VS CPU使用率
* CPU 密集型进程，使用大量 CPU 会导致平均负载升高，此时这两者是一致的；
* I/O 密集型进程，等待 I/O 也会导致平均负载升高，但 CPU 使用率不一定很高；
* 大量等待 CPU 的进程调度也会导致平均负载升高，此时的 CPU 使用率也会比较高。

安装压测工具
```shell
yum install stress -y
```

安装监控工具(包含 mpstat pidstat)
```shell
yum install sysstat -y
```
* mpstat 是一个常用的多核 CPU 性能分析工具，用来实时查看每个 CPU 的性能指标，以及所有 CPU 的平均指标。
* pidstat 是一个常用的进程性能分析工具，用来实时查看进程的 CPU、内存、I/O 以及上下文切换等性能指标。


### 测试01 CPU 密集型进程
压测命令
```
$ stress --cpu 1 --timeout 600

参数
-c, --cpu N 产生 N 个进程，每个进程都反复不停的计算随机数的平方根
-i, --io N 产生 N 个进程，每个进程反复调用 sync() 将内存上的内容写到硬盘上
-m, --vm N 产生 N 个进程，每个进程不断分配和释放内存
–vm-bytes B 指定分配内存的大小
–vm-stride B 不断的给部分内存赋值，让 COW(Copy On Write)发生
–vm-hang N 指示每个消耗内存的进程在分配到内存后转入睡眠状态 N 秒，然后释放内存，一直重复执行这个过程
–vm-keep 一直占用内存，区别于不断的释放和重新分配(默认是不断释放并重新分配内存)
-d, --hadd N 产生 N 个不断执行 write 和 unlink 函数的进程(创建文件，写入内容，删除文件)
–hadd-bytes B 指定文件大小
-t, --timeout N 在 N 秒后结束程序
–backoff N 等待N微妙后开始运行
-q, --quiet 程序在运行的过程中不输出信息
-n, --dry-run 输出程序会做什么而并不实际执行相关的操作
–version 显示版本号
-v, --verbose 显示详细的信息
```

监控
```shell
# -d 参数表示高亮显示变化的区域
$ watch -d uptime

# -P ALL 表示监控所有 CPU，后面数字 5 表示间隔 5 秒后输出一组数据
$ mpstat -P ALL 5

# 间隔 5 秒后输出一组数据
$ pidstat -u 5
```

另外可以结合 top 命令一起看下
* 按1 显示所有cpu
* 按c 显示命令行详情

### 测试02 I/O 密集型进程 (使用了 sync() 系统调用)
压测命令
```shell
stress -i 1 --timeout 600
```
监控
```shell
$ watch -d uptime
$ mpstat -P ALL 5
$ pidstat -u 5
```
### 测试03 大量进程场景
压测命令
```shell
$ stress -c 8 --timeout 600
```
监控
```shell
$ watch -d uptime
$ pidstat -u 5
```


## 03基础篇：经常说的 CPU 上下文切换是什么意思？（上）


## 04基础篇：经常说的 CPU 上下文切换是什么意思？（下）

## 06
* 短时监控工具
  * execsnoop
  * perf top
