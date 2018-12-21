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

![四次挥手](https://github.com/chuanlei/tech-notes/blob/master/pics/four-handshake.jpg)

## 滑窗结构

![发送滑窗](https://github.com/chuanlei/tech-notes/blob/master/pics/sender-window.png)

![接收滑窗](https://github.com/chuanlei/tech-notes/blob/master/pics/receiver-window.png)

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

![堵塞窗口](https://github.com/chuanlei/tech-notes/blob/master/pics/congestion-window.png)

## tcp的几大模块

#### 分段与流
#### 滑窗
#### 连接
#### 流量控制
#### 重新发送
#### 堵塞控制
## DNS

DNS服务器通过DNS协议相互通信，该协议主要基于UDP（是一个应用层协议）

```
cat /etc/resolv.conf
```
操作系统中的域名解析模块(DNS Resolver)负责域名解析的相关工作

## http

![http请求与返回](https://github.com/chuanlei/tech-notes/blob/master/pics/request-response.png)

HTTP协议规定了请求和回复需要遵循的格式。请求和回复需要满足下面的格式:
```
起始行 (start line)
头信息 (headers)

主体(entity body)
```

起始行只有一行。它包含了请求/回复最重要的信息。请求的起始行表示(顾客)“想要什么”。回复的起始行表示(后厨)“发生什么”。
头信息可以有多行。每一行是一对键值对(key-value pair)，比如:
Content-type: text/plain 它表示，包含有一个名为Content-type的参数，该参数的值为text/plain。头信息是对起始行的补充。请求的头信息对服务器有指导意义 (好像在菜单上注明: 鸡腿不要辣)。回复的头信息则是提示客户端（比如，在盒子上注明: 小心烫）

## DHCP协议（动态主机设置协议）

DHCP协议用于动态的配置电脑的网络相关参数，如主机的IP地址，路由器出口地址、DNS域名服务器地址等。一台电脑只要接上网，就可以通过DHCP协议获得相关配置，从而顺利的畅游网络。

在网络通信中，一台电脑需要设置自己的IP地址等网络参数。可对于电脑用户来说，这些设置太过复杂。幸好，DHCP协议可以解决这一让人头痛的问题。

通常来说，普通电脑中都内置有DHCP客户端模块。电脑接上网络后，DHCP客户端发现新连通的网络，会在该网络上找DHCP服务器。DHCP服务器将给电脑提供合理的网络配置，并把设置信息传回本机。所谓的DHCP服务器，其实就是一些运行有DHCP服务器端软件的特殊电脑。他们像等候在网络上的服务员，为新来的顾客排忧解难。本机和DHCP服务器之间的通信，都是通过DHCP协议进行的。

在此时，通信基本靠吼

## TLS协议

TLS名为传输层安全协议(Transport Layer Protocol)，这个协议是一套加密的通信协议。它的前身是SSL协议(安全套接层协议，Secure Sockets Layer)。这两个协议的工作方式类似，但TLS协议针对SSL协议进行了一些改善。SSL/TLS协议利用加密的方式，在开放的互联网环境中实现了加密通信，让通信的双方可以安心的说悄悄话

## CIDR与NAT

```
$ ipcalc 199.165.145.254/31
Address:   199.165.145.254      11000111.10100101.10010001.1111111 0
Netmask:   255.255.255.254 = 31 11111111.11111111.11111111.1111111 0
Wildcard:  0.0.0.1              00000000.00000000.00000000.0000000 1
=>
Network:   199.165.145.254/31   11000111.10100101.10010001.1111111 0
HostMin:   199.165.145.254      11000111.10100101.10010001.1111111 0
HostMax:   199.165.145.255      11000111.10100101.10010001.1111111 1
Hosts/Net: 2                     Class C, PtP Link RFC 3021
```

NAT是为私有网络(private network)服务的。该网络中的主机使用私有IP地址。当私有网络内部主机和外部Internet通信时，网关(gateway)路由器负责将私有IP地址转换为全球IP地址，这个地址转换过程就是Network Address Translation。网关路由器的NAT功能。最极端情况下，我们可以只分配一个全球IP地址给网关路由器，而私有网络中的设备都使用私有IP地址。由于私有IP地址可以在不同私有网络中重复使用，所以就大大减小了设备对IP地址的需求。

### 基础NAT
![基础nat](https://github.com/chuanlei/tech-notes/blob/master/pics/basic-nat.jpg)

### NATP
NAT还有一种，被成为NAPT (Network Address and Port Translation)。在基础NAT中，高层协议的端口号并不会改动。NAPT下，IP地址和端口号可能同时改动。
