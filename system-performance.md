## 短时进程

## 进程状态

### 不可中断状态进程

### 僵尸进程

`sigchild`

### 工具

dstat(vmstat, iostat, sysstat等)

```shell
yum install docker.x86_64 dstat sysstat #centos 7
service docker start
docker run --privileged --name=app -itd feisky/app:iowait
```

## 中断

硬中断+软中断(ksoftirqd)

软中断CPU使用率过高

sar, hping3, tcpdump

syn flood攻击

```shell
hping3 -S -p 80 -i u100 ${host}
```

## 性能指标的量化
1. 系统指标(CPU绑定、CPU独占、Nice调整优先级、NUMA优化)
2. 应用指标(编译器优化、异步处理)

## stress-ng模拟iowait

## sysbench模拟上下文切换

## perf命令
```shell
 perf record
 perf report
```

### 用perf分析java程序
perf-events已经支持了JIT，但是还需要`/tmp/perf-PID.map`文件进行符号翻译。

perf-map-agent生成符号表

JVM需要开启JDK选项`-XX:+PreserveFramePointer`

参考资料 Java in flames

## 内存

### 基础知识
虚拟内存（由内核虚构出来，页表在MMU中）

TLB即为MMU中的高速缓存

Linux使用四级页表管理内存页。

用户空间内存：只读段、数据段、堆、栈、文件映射段、栈

malloc(小：brk, 大：mmap)

伙伴系统

slab相当于伙伴系统之上的一个缓存。

VIRT, RES, SHR, %MEM

### buffer与cache
`free`的输出是通过解析`/proc/meminfo`得到的。

buffer是磁盘数据的缓存

cached是文件系统的数据缓存

使用`dd`模拟磁盘和文件的I/O

清理系统缓存
```shell
echo 3 > /proc/sys/vm/drop_caches
```

```shell
dd if=/dev/urandom of=/tmp/file bs=1M count=500
```

### 缓存的性能

`cachestat`与`cachetop`

利用了`eBPF`机制（内核版本4.1以上）

`bcc-tools`

指定文件的缓存大小`pcstat`

### 内存泄露
`memleak`命令是bcc软件包中的一个工具

### swap工作原理
怎么衡量内存是不是紧张？

1. 直接内存回收
2. `kswapd0`

```shell
sar -r -S 1
```

#### NUMA架构

```shell
numactl --hardware
cat /proc/zoneinfo
```









 






