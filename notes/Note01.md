# Chapter 1: 计算机网络和因特网

[TOC]

### 课程主线

* 路由器和交换机的工作过程
  * 路由器：在不同的网络间转发数据
  * 交换机：在同一个网络内部转发数据
* 网络中数据的流动
  * 流动方式：一个具体的协议中，到底谁在发送和接收数据，数据包经过了哪些节点
  * 组织形式：数据本身的传输格式

### 初步理解计算机网络

```
Application   应用层
------------
   TCP/UDP    传输层
O ----------
S    IP       网络层
   Driver
------------
Network card  链路层
```

---

端系统通过通信链路和交换机、路由器连接在一起。

* 端系统：APP + OS + 网络接口（i.e. 网卡）
  * 电脑、手机、联网电器等
* 通信链路：有线（光纤等） + 无线（无线电等）
* ［链路层］交换机：子网内部进行数据转发
* 路由器：不同子网之间进行数据转发

---

接口具有唯一的 IP 地址，子网内部通过物理地址（MAC）进行通信。OS 为了区分不同应用程序发送的数据流，还会使用端口号进行标识。

* IP + MAC => 一台主机
* Port => 特定主机上的应用程序
* IP: 子网地址（掩码） + 主机地址
  * 相同子网：子网地址相同；直接相连，没有中间路由器

* 默认网关：通往外部网络的第一跳路由器

### 什么是 Internet

##### 组成

* 成千上万联网计算设备
  * 主机(hosts) = 端系统(end systems)
* 通信链路：光纤、铜线、无线电（带宽，dps）
* 路由：前向转发数据报
  * 路由器、链路层交换机
* ISP：内容服务提供商
* 协议：控制发送和接收信息
  * e.g., TCP, IP, HTTP, Ethernet
* Internet：网络的网络（层次结构）
  * 标准：RFC (Request for comments)

##### 服务

* 通信架构：使分布式应用成为可能
  * Web, VoIP, email, games, file sharing
* 通信服务：提供给应用，尽最大努力为主机提供可靠通信
* 编程接口：有网络的端系统为应用程序提供了编程接口，规定了如何请求网络提供一个传输服务

##### 协议

协议定义了：发送信息的格式、发送/接收消息的次序、发送/接收消息后采取的动作

### 网络架构

* 网络架构
  * 网络边缘：应用和主机
  * 接入网，物理介质：有线+无线，通信链路
    * 接入网带宽，专用/共享
  * 网络核心：互联的路由，网络的网络

##### 网络边缘

端系统：运行终端程序，在网络边缘

* C/S 模式：客户机发出请求，一直开机的服务器接收相应的回复
  * Web browser/server; email client/ server
* P2P 模式：很少使用服务器
  * Skype, BitTorrent

##### 接入网

* 住宅接入网（点到点访问）
  * 使用 调制解调器（modem，俗称*猫*）拨号，已过时
  * ADSL（数据用户线）：电信公司接入，新型电话接入技术
    * 上行较慢，下行较快（通过频分复用技术将电话线分成三个不重叠的频率段）
    * FDM（频分多路复用）
  * 光纤接入：AON（点对点），PON（点对多点，拓扑技术）
* 公司接入（LAN，局域网）
  * Ethernet
* 无线接入网络
  * 无线LANs：WiFi 距离短
  * 广域无线接入：4G，5G

家庭网络组成：DSL，路由/防火墙/NAT，Ethernet，无线 AP(Access Point)

##### 物理介质

* Bit：在接受和发送者之间传播
* 物理链路：接收和发送者之间的连接
* guided media：引导型介质，在固态介质中传播
  * 双绞线、同轴电缆
* unguided media：非引导型介质，信号自由传播
  * 无线电

---

TP（双绞线）：四对绝缘线，蓝皮网线

同轴电缆（电视线）：两根同轴的铜线，双向传输

光纤：玻璃光纤，承载光信号，高速传输

无线电：电磁波承载信号，没有物理线路，易受环境影响

### 网络核心

##### 交换方式

> 路由器构成的网络

数据如何通过网络交换：

* 电路交换 circuit switching：对呼叫进行端到端资源保留
  * 专用线路
  * 电话网络
* 报交换 packet switching：每个端到端数据流都被划分为 "packets"
  * 数据封装在独立的 chunks 中
  * 可容纳更多的用户

##### 层次化架构

* 核心："tier-1" ISPs
  * 国际性覆盖
  * AT&T, Verizon ...
* "tier-2" ISPs
  * 国家性覆盖
  * 中国电信、中国移动
* "tier-3" ISPs
  * 最后一跳网络（Access Network），与用户相连
  * 杭州电信

### 时延、丢包和吞吐率

##### 时延

数据报延迟的四个来源：

1. 节点处理延迟（nodal processing）：检查比特错误、决定输出链路
2. 排队（queuing）：在输出链路等待传输，依赖于路由器拥塞程度<br/>数据报在路由缓冲区中排队（数据报到达速率超过链路容量；数据报排队等待发送）
3. 传输延迟（Transmission Delay）：L/R，低速链路明显
4. 传播延迟（Propagation Delay）：几~几百 微秒

**排队延迟**估计：

* R=带宽(bps)
* L=数据报长(bits)
* a=平均到达速率

流量密度=La/R（0~1：延迟越来越大，>1：延迟无穷大）

##### 丢包

队列所在内存有限，队列满了后到达的数据包会丢失。丢失的数据包可能重传，也可能不被重传。

##### 吞吐率

Throughput：发送者与接收者之间数据传输的速率（bits/s）

### 协议层次、服务模型

Layers：每一层次都实现一定功能 => 简化处理、便于维护

##### Internet 协议栈

Message => Segment（报文段） => datagram（数据报） => frame（帧）

```
应用层: HTTP, SMTP, FTP    | application |
传输层: TCP, UDP           | transport   |
网络层: IP                 | network     |
链路层: Ethernet           | link        |
物理层: bits "on the wire" | physical    |
```

ISO/OSI 参考模型：

```
application
  presentation: 表示层，允许应用程序解释数据含义
  session     : 会话层，同步、数据恢复、会话状态
transport
network
link
physical
```

##### 分组的封装

1. 应用程序通过调用 OS 提供的 Network API 将 *message* 传递给 *传输层* 模块
2. OS 中的 *传输层* 软件模块给消息添加一个头部后（以区别不同的**应用程序**），调用 *网络层* 软件模块的编程接口，将报文段 *segment* 传递给 *网络层*。
3. *网络层* 软件模块也是位于 OS 中，它给报文段又添加了一个头部后(以区别不同的**主机**)，调用网卡 Driver 的编程接口，将数据报 *datagram* 传递给网卡。
4. 可以认为位于 OS 中的网卡 Driver和网卡硬件构成了 *链路层*，它给数据报添加帧 *frame* 头部后（以区别不同的网卡），发送进入了网络。
5. 链路层交换机实现了网络协议第一层（*物理层*）和第二层（*链路层*），在同一个局域网内转发帧 *frame*。帧在同一个局域网的计算机之间、计算机和路由器、路由器和路由器之间直接交付。
6. 路由器实现了网络协议的第一层（物理层）到第三层（网络层），在不同的网络间转发数据报 *datagram*。<br/>路由器是一种具有多个网络接口的设备，在不同的网络中都有接口，负责收发数据。数据报是封装在帧中转发的。路由器收到一个帧后，提取出数据报，向前转发时又重新封装成帧 （此时链路层发送/接收地址改变了）。
7. 链路层交换机转发帧、路由器转发数据报都是通过查询转发表来实现的。
8. 在接收端，帧被解封装，提取出数据报 *datagram*，又从数据报中取出报文段 *segment*，最后提取出消息 *message* 交付给接收应用程序。

### 网络安全

1. 如何攻击
2. 如何防卫
3. 如何设计网络架构以防御攻击

Internet 设计之初没有考虑安全问题，所以后来不断打补丁。

* 恶意软件
  * Trojan horse：控制另一台计算机的一段特定程序
  * Worm：可传播拷贝到其他计算机系统
  * Virus：破坏计算机功能或数据的代码
* 服务器和网络架构的攻击
  * 拒绝服务攻击，Denial of service（Dos）：攻击者使用过量的服务请求或流量，使得资源不可用
    1. 选择目标
    2. 闯入周边主机
    3. 发送数据包到目标主机
  * 数据报的监听，Packet sniffing
  * 重放攻击，record and playback
