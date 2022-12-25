# Chapter 2: 应用层

>应用软件通常在网络边缘的端系统中运行，而不是在网络核心的交换机和路由器上运行。

[TOC]

### 网络应用的原理

##### 应用架构

* Client-Server (CS)：服务器永远 alive，客户端间歇性与服务器通信
* Peer-to-peer (P2P)：任意端节点主机之间可以相互通信
* Hybrid of CS and P2P：中央服务器提供 IP 地址，各用户之间 p2p 通信
  * QQ, Skype

##### 协议原理

* 进程通信：不同主机上的进程间通过交换 messages 通信 => Client Process & Server Process
* 进程寻址：进程接收信息，需要一个标识符 => IP（主机地址） + Port（进程标识）
  * HTTP server: 80
  * Mail server: 25
* Socket 套接字：进程通过其接收或发送信息
  * 选择传输层协议，指定某些参数

---

应用层协议的定义：

* 交换消息类型
* 消息类型语法
* 字段语义
* 进程发送/响应消息的规则

=> 共有协议（HTTP, SMTP），私有协议（Skype，QQ）

需要的传输层服务：数据丢失、时效性、吞吐量、安全性

### Web 与 HTTP

##### HTTP 概述

> Web page：由对象组成，可以是 HTML, JPEG...（每个 object 由 URL 寻址）

HTTP：超文本传输协议，hypertext transfer protocol

* Web 应用层协议
* CS 架构：浏览器 & Web 服务器
* 使用 TCP：
  1. client 初始化 TCP 连接
  2. server 接收 client 的 TCP 连接请求
  3. HTTP 消息在二者之间交换
  4. TCP 连接关闭
* HTTP 是无状态的：不保存之前连接的状态
* HTTP 连接
  * 非持久连接（HTTP 1.0）：每次连接最多发送一个 object
  * 持久连接（HTTP 1.1）：可发送多个 object

##### HTTP 消息

* Request
  * GET：下载（input 包含在 URL 字段中）
  * POST：上载（input 包含在 entity body 中）
  * PUT：在 entity body 包含上传文件，上传至 URL 字段指定位置
  * DELETE：删除 URL 字段指定的文件

```
GET /hello HTTP/1.1
Host: www.hitsz.edu.sz
User-agent: Mozilla/4.0
Connection: close

```

* Response

```
HTTP/1.1 200 OK
Connection close
Date: Thu, 06 Aug 1998 12:00:15 GMT
Server: Apache/1.3.0 (Unix)
Content-Length: 6821
Content-Type: text/html

...data...
```

##### cookie 和缓存

引入 Cookies 以保存用户状态：

1. HTTP 响应消息头部
2. HTTP 请求消息头部
3. 保留在客户端的 cookie 文件，由 browser 管理
4. Web 站点后台数据库

可用于 认证、保存会话状态，服务器扫描 C盘 的 cookie 文件可获得大量信息。

---

Web cache（proxy server 代理服务器）

* Goal：不访问 origin server 就可满足 client request
  * 代理服务器保存 origin server 的数据

cache 可运行在 客户端 和 服务端，通常由 ISP 部署

### 电子邮件

> 异步的通信媒体

组成部分：

* 用户代理
  * 邮件客户端 outlook，浏览器
* 邮件服务器
  * 邮箱：保存用户邮件
  * 邮箱队列：等待发送的邮件
* 邮件传输协议：SMTP, POP3...

---

* SMTP：25 port，使用 TCP 从客户端可靠地**发送**邮件信息到服务器
  * 持久连接
  * 使用 ASCII 编码（图片使用 base64 进行编码，得到字符串 => MIME 扩展）
  * HTTP pull，STMP push；HTTP 每个对象都有响应信息，SMTP 多个对象在同一个消息中发送
* Mail access protocol：从邮件服务器**获取**邮件
  * POP3：认证和下载
  * IMAP：更多特征，能在服务器上对邮件做某些操作
  * HTTP

### DNS

##### DNS 概述

DNS：53 port，域名系统，Domain Name System（消息交换使用 UDP 协议）

* 分布式/层次化数据库
* 应用层协议，互相通信完成名字解析

DNS 服务：

* 主机到 IP 地址的翻译
* 主机别名：对外公开的名字（一个主机可以有多个别名，可用于替换）
* 邮件服务器别名
* 负载均衡

##### DNS 工作过程

客户端 get host by name -> Windows 本地的 DNS client -> local DNS server -> root DNS server -> ... -> return IP address

DNS 记录：分布式存储数据库记录

DNS 协议：query & reply，内部 id & flags

### Socket 编程

##### 概述

独立于具体协议的通用网络编程接口，位于 会话层 和 传输层 之间，位于应用程序与 OS 之间；可视作不同主机进程之间的文件描述符。

* SOCK_STREAM：面向连接，可靠的数据传输服务 -- TCP
* SOCK_DGRAM：提供无连接服务，以独立数据包形式发送 -- UDP
* SOCK_RAW：对较低层次协议直接访问 -- IP

##### 函数介绍

* 网络字节序：TCP/IP 中规定的一种数据表示格式
* 主机字节序：与具体 CPU, OS 有关

---

Linux Socket (BSD Socket)

* socket：创建套接字
* bind：绑定本机端口
* connect：客户端建立连接（三次握手）
* listen：服务端监听端口
* accept：服务端接受连接（从等待连接队列中抽取第一个连接，创建同类的新套接字并返回句柄）
* recv, recvfrom：数据接收
* send, sendto：数据发送
* close, shutdown（读写通道）：关闭连接
* 转换函数
  * IP 地址转换函数：inet_addr, inet_ntoa
  * 字节排序函数
* 网络信息检索函数：gethostbyname（由 DNS client 完成）

#####  TCP/IP 网络程序框架

面向连接的 C/S 工作流程：

* 服务端：socket -> bind -> listen -> accept -> send/recv [-> shutdown] ->close
* 客户端：socket [-> bind] -> connect -> send/recv -> close

无连接的 C/S 工作流程：

* 服务端：socket -> bind -> sendto/recvfrom -> close
* 客户端：socket -> bind -> sendto/recvfrom -> close

---

阻塞通信：Socket 进行 I/O 操作时，函数要等到相关操作完成后才能返回

非阻塞通信：调用立即返回

