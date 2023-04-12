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
stress压测命令
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

stress升级版本 stress-ng
```

pidstat命令
```
-u：默认的参数，显示各个进程的cpu使用统计
-r：显示各个进程的内存使用统计
-d：显示各个进程的IO使用情况
-p：指定进程号
-w：显示每个进程的上下文切换情况
-t：显示选择任务的线程的统计信息外的额外信息
-T { TASK | CHILD | ALL }
这个选项指定了pidstat监控的。TASK表示报告独立的task，CHILD关键字表示报告进程下所有线程统计信息。ALL表示报告独立的task和task下面的所有线程。
注意：task和子线程的全局的统计信息和pidstat选项无关。这些统计信息不会对应到当前的统计间隔，这些统计信息只有在子线程kill或者完成的时候才会被收集。
-V：版本号
-h：在一行上显示了所有活动，这样其他程序可以容易解析。
-I：在SMP环境，表示任务的CPU使用率/内核数量
-l：显示命令名和所有参数
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
```shell
# 每隔 5 秒输出 1 组数据
$ vmstat 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 7005360  91564 818900    0    0     0     0   25   33  0  0 100  0  0
```
* cs（context switch）是每秒上下文切换的次数。
* in（interrupt）则是每秒中断的次数。
* r（Running or Runnable）是就绪队列的长度，也就是正在运行和等待 CPU 的进程数。
* b（Blocked）则是处于不可中断睡眠状态的进程数。

```shell
# 每隔 5 秒输出 1 组数据
$ pidstat -w 5
Linux 4.15.0 (ubuntu)  09/23/18  _x86_64_  (2 CPU)
 
08:18:26      UID       PID   cswch/s nvcswch/s  Command
08:18:31        0         1      0.20      0.00  systemd
08:18:31        0         8      5.40      0.00  rcu_sched
```
* 一个是 cswch ，表示每秒自愿上下文切换（voluntary context switches）的次数
  * 所谓**自愿上下文切换，是指进程无法获取所需资源，导致的上下文切换**。比如说， I/O、内存等系统资源不足时，就会发生自愿上下文切换。
* 另一个则是 nvcswch ，表示每秒非自愿上下文切换（non voluntary context switches）的次数
  * 而**非自愿上下文切换，则是指进程由于时间片已到等原因，被系统强制调度，进而发生的上下文切换**。比如说，大量进程都在争抢 CPU 时，就容易发生非自愿上下文切换。

压测工具sysbench
```shell
安装
yum install sysbench -y

# 以 10 个线程运行 5 分钟的基准测试，模拟多线程切换的问题
$ sysbench --threads=10 --time=300 threads run
```
监控
```shell
# 每隔 1 秒输出 1 组数据（需要 Ctrl+C 才结束）
$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 6  0      0 6487428 118240 1292772    0    0     0     0 9019 1398830 16 84  0  0  0
 8  0      0 6487428 118240 1292772    0    0     0     0 10191 1392312 16 84  0  0  0
 
r 列增加
in 终端增加
cs 切换增加
sy 系统使用率很高
```

```shell
# 每隔 1 秒输出 1 组数据（需要 Ctrl+C 才结束）
# -w 参数表示输出进程切换指标，而 -u 参数则表示输出 CPU 使用指标
$ pidstat -w -u 1
08:06:33      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
08:06:34        0     10488   30.00  100.00    0.00    0.00  100.00     0  sysbench
08:06:34        0     26326    0.00    1.00    0.00    0.00    1.00     0  kworker/u4:2
 
08:06:33      UID       PID   cswch/s nvcswch/s  Command
08:06:34        0         8     11.00      0.00  rcu_sched
08:06:34        0        16      1.00      0.00  ksoftirqd/1
08:06:34        0       471      1.00      0.00  hv_balloon
08:06:34        0      1230      1.00      0.00  iscsid
08:06:34        0      4089      1.00      0.00  kworker/1:5
08:06:34        0      4333      1.00      0.00  kworker/0:3
08:06:34        0     10499      1.00    224.00  pidstat
08:06:34        0     26326    236.00      0.00  kworker/u4:2
08:06:34     1000     26784    223.00      0.00  sshd
```

显示线程-t情况
```shell
# 每隔 1 秒输出一组数据（需要 Ctrl+C 才结束）
# -wt 参数表示输出线程的上下文切换指标
$ pidstat -wt 1
08:14:05      UID      TGID       TID   cswch/s nvcswch/s  Command
...
08:14:05        0     10551         -      6.00      0.00  sysbench
08:14:05        0         -     10551      6.00      0.00  |__sysbench
08:14:05        0         -     10552  18911.00 103740.00  |__sysbench
08:14:05        0         -     10553  18915.00 100955.00  |__sysbench
08:14:05        0         -     10554  18827.00 103954.00  |__sysbench
...
```

### 小结
* 今天，我通过一个 sysbench 的案例，给你讲了上下文切换问题的分析思路。
* 碰到上下文切换次数过多的问题时，我们可以借助 vmstat 、 pidstat 和 /proc/interrupts 等工具，来辅助排查性能问题的根源。

## 06
* 短时监控工具
  * execsnoop
  * perf top




top 命令
* 按1 显示所有cpu
* 按c 显示命令行详情