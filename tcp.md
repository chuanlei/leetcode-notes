# tcp常见知识点

## 参考资料

[谈谈 TCP 的 TIME_WAIT](https://zhenbianshu.github.io/2018/12/talk_about_tcp_timewait.html?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

[线上大量CLOSE_WAIT原因排查](https://mp.weixin.qq.com/s/NZqNXdv0esCCUFDYtDy_Hw)

[协议森林](https://wizardforcel.gitbooks.io/protocol-forest-vamei/content/index.html)

## 问题

我们进程怎么决定使用哪个网卡发送数据呢？

OS中的IP模块如何实现？

## 总结

ICMP协议是IP协议的排错帮手。

我们可以将UDP协议看作IP协议暴露在传输层的一个接口（只是多了端口的概念）。

socket是传输层的概念（让内核来接收网络协议的细节）

TCP：流通信的意义和实现方式

“流”的要点是次序，实现这一点可不简单。tcp封装到ip包中的是一个片段(segment)

片段与编号

滑窗的价值在于提高效率

利用缓存保留一些“不那么乱”的片段，期望能在段时间内补充上之前的片段(暂不处理，但发送相应的ACK)；对于“乱”的比较厉害的片段，则将它们拒绝(不处理，也不发送对应的ACK)

TCP协议有实时调整滑窗大小的算法，以实现最优效率。

建立TCP连接的目的是：让连接的双方交换初始序列号（通过SYNC片段实现）

连接的终结使用的是特殊片段FIN（在断开连接的过程中，使用了四片信息，TCP并没有合并FIN与ACK片段。原因是TCP连接允许单向关闭(half-close)

在Client发送出最后的ACK回复，但该ACK可能丢失。Server如果没有收到ACK，将不断重复发送FIN片段。所以Client不能立即关闭，它必须确认Server接收到了该ACK。Client会在发送出ACK之后进入到TIME_WAIT状态。Client会设置一个计时器，等待2MSL的时间。如果在该时间内再次收到FIN，那么Client会重发ACK并再次等待2MSL。所谓的2MSL是两倍的MSL(Maximum Segment Lifetime)。MSL指一个片段在网络中最大的存活时间，2MSL就是一个发送和一个回复所需的最大时间。如果直到2MSL，Client都没有再次收到FIN，那么Client推断ACK已经被成功接收，则结束TCP连接。

[四次挥手](https://github.com/chuanlei/tech-notes/blob/master/pics/four-handshake.jpg)

## 滑窗结构

[发送滑窗](https://github.com/chuanlei/tech-notes/blob/master/pics/sender-window.png)

[接收滑窗](https://github.com/chuanlei/tech-notes/blob/master/pics/receiver-window.png)

### 流量控制

流量控制(flow control)是指接收方将advertised window的大小通知给发送方，从而指导发送方修改offered window的大小。接收方将该信息放在TCP头部的window size区域

### 超时重新发送(基本形式)

这个计时等待的时间叫做重新发送超时时间(RTO, retransmission timeout)

```
RTO = mean + 4 std
```

### 快速重新发送(高级形式)

TCP协议规定，当接收方收到乱序片段的时候，需要重复发送ACK。

### 阻塞（congestion）控制

这是一个公德心的问题

发送方需要有公德心

TCP还会维护一个堵塞窗口 大小，以根据网络状况来调整滑窗大小。真实滑窗大小取这两个滑窗限制的最小值，从而同时满足两个限制 (流量控制和堵塞控制)。

堵塞窗口(congestion window)总是处于两种状态的一个。这两种状态是: 慢启动(slow start)和堵塞规避(congestion avoidance)。

[堵塞窗口](https://github.com/chuanlei/tech-notes/blob/master/pics/congestion-window.png)






