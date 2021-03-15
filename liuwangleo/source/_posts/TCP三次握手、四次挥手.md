---
title: TCP三次握手、四次挥手
date: 2021-03-15 13:18:27
tags:
    - TCP
    - 三次握手
    - 四次挥手
categories:
    - 计算机网络
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.taopic.com%2Fuploads%2Fallimg%2F130513%2F240422-1305130F30216.jpg&refer=http%3A%2F%2Fimg.taopic.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1618374397&t=0c801f642fbc728c55bce6c4bd956603
---

# TCP协议
## TCP协议是什么
`传输控制协议`（Transmission Control Protocol，TCP）是一种`面向连接的`、`可靠的`、`基于字节流`的传输层通信协议。在 TCP 协议中，通过三次握手建立连接。通信结束后，还需要断开连接。如果在发送数据包时，没有正确被发送到目的地时，将会重新发送数据包
## TCP协议的作用
TCP 协议是以面向连接的方式进行通信的，其作用如下：
- 面向流的处理：TCP 以流的方式处理数据。换句话说，TCP 可以一个字节一个字节地接收数据，而不是一次接收一个预订格式的数据块。
- TCP 把接收到的数据组成长度不等的段，再传递到网际层。
- 重新排序：如果数据以错误的顺序到达目的地，TCP 模块能够对数据重新排序，来恢复原始数据。
- 流量控制：TCP 能够确保数据传输不会超过目的计算机接收数据的能力。
- 优先级与安全：为 TCP 连接设置可选的优先级和安全级别。
- 适当的关闭：以确保所有的数据被发送或接收以后，再进行关闭连接。

## TCP工作模式
TCP 协议的数据包进行传输采用的是服务器端和客户端模式。发送 TCP 数据请求方为客户端，另一方则为服务器端。客户端要与服务器端进行通信，服务器端必须开启监听的端口，客户端才能通过端口连接到服务器，然后进行通信。

# TCP三次握手
所谓三次握手（Three-Way Handshake）即建立TCP连接，就是指建立一个TCP连接时，需要客户端和服务端总共发送3个包以确认连接的建立。在socket编程中，这一过程由客户端执行connect来触发，整个流程如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315103134276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)
（1）第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。
（2）第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，Server进入SYN_RCVD状态。
（3）第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了。
**简单来说，就是**
1、建立连接时，客户端发送SYN包（SYN=i）到服务器，并进入到SYN-SEND状态，等待服务器确认
2、服务器收到SYN包，必须确认客户的SYN（ack=i+1）,同时自己也发送一个SYN包（SYN=k）,即SYN+ACK包，此时服务器进入SYN-RECV状态
3、客户端收到服务器的SYN+ACK包，向服务器发送确认报ACK（ack=k+1）,此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手，客户端与服务器开始传送数据。

## SYN攻击
> 在三次握手过程中，`Serve`r发送`SYN-ACK`之后，收到`Client`的`ACK`之前的TCP连接称为`半连接`（half-open connect），此时`Server`处于`SYN_RCVD`状态，当收到`ACK`后，Server转入`ESTABLISHED`状态。SYN攻击就是Client在短时间内`伪造`大量不存在的`IP地址`，并向Server不断地发送`SYN`包，Server回复确认包，并等待`Client`的确认，由于源地址是不存在的，因此，Server需要不断重发直至超时，这些伪造的`SYN`包将产时间占用未连接队列，导致正常的SYN请求因为队列满而被丢弃，从而引起网络堵塞甚至系统瘫痪。`SYN攻击`时一种典型的`DDOS攻击`，检测SYN攻击的方式非常简单，即当Server上有大量半连接状态且源IP地址是随机的，则可以断定遭到SYN攻击了，使用如下命令可以让之现行：
> **netstat -nap | grep SYN_RECV**

## 为什么进行三次握手，而不是两次或者四次
`为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。`
> 比如：`client`发出的第一个`连接请求`报文段并`没有丢失`，而是在某个网络结点`长时间`的`滞留`了，以致延误到连接释放以后的某个时间才到达`server`。本来这是一个早已`失效`的报文段，但是server收到此失效的连接请求报文段后，就误认为是`client`再次发出的一个新的连接请求，于是就向`client`发出`确认报文段`，同意建立连接。假设不采用“三次握手”，那么只要`server`发出`确认`，新的`连接就建立`了，由于client并没有发出建立连接的请求，因此不会理睬`server`的确认，也不会向`serve`r发送数据，但`server`却以为新的运输`连接`已经`建立`，并一直`等待``client`发来数据。所以没有采用“三次握手”，这种情况下server的很多`资源`就白白`浪费`掉了。

**简单来说，三次握手的过程是客户端与服务段交换第一个包序列号的过程**

# TCP四次挥手
所谓`四次挥手`（Four-Way Wavehand）即`终止TCP连接`，就是指断开一个TCP连接时，需要客户端和服务端总共发送4个包以确认连接的断开。在socket编程中，这一过程由客户端或服务端任一方执行close来触发，整个流程如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210315113419321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NTgxOTYx,size_16,color_FFFFFF,t_70)
由于TCP连接时`全双工`的，因此，每个方向都必须要`单独进行关闭`，这一原则是当一方完成数据发送任务后，发送一个`FIN`来终止这一方向的连接，收到一个`FIN`只是意味着这一方向上没有数据流动了，即不会再收到数据了，但是在这个TCP连接上仍然能够发送数据，直到这一方向也发送了`FIN`。首先进行关闭的一方将执行主动关闭，而另一方则执行被动关闭，上图描述的即是如此。

（1）第一次挥手：`Client`发送一个`FIN`，用来关闭`Client`到`Server`的数据传送，Client进入`FIN_WAIT_1`状态。

（2）第二次挥手：`Server`收到`FIN`后，发送一个`ACK`给`Client`，确认序号为收到`序号+1`（与SYN相同，一个FIN占用一个序号），Server进入`CLOSE_WAIT`状态。

（3）第三次挥手：`Serve`r发送一个`FIN`，用来关闭`Server`到`Client`的数据传送，`Server`进入`LAST_ACK`状态。

（4）第四次挥手：`Client`收到`FIN`后，`Client`进入`TIME_WAIT`状态，接着发送一个`ACK`给`Server`，确认序号为收到`序号+1`，`Server`进入`CLOSED`状态，完成四次挥手。

## 为什么需要四次挥手呢？
TCP是`全双工模式`，当`client`发出`FIN`报文段时，只是表示client已经没有数据要发送了，client告诉server，它的数据已经全部发送完毕了；但是，这个时候`client`还是可以接受来`server`的数据；当`server`返回`ACK`报文段时，表示它已经知道`client`没有数据发送了，但是`server`还是可以发送数据到`client`的；当`server`也发送了`FIN`报文段时，这个时候就表示`server`也没有数据要发送了，就会告诉`client`，我也没有数据要发送了，如果收到`client`确认报文段，之后彼此就会愉快的中断这次TCP连接。

## 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？
这是因为服务端在`LISTEN`状态下，收到建立连接请求的`SYN`报文后，把`ACK和SYN`放在一个报文里发送给客户端。而`关闭连接`时，当收到对方的`FIN`报文时，仅仅表示对方不再发送数据了但是还能接收数据，己方也未必全部数据都发送给对方了，所以己方可以立即`close`，也可以发送一些数据给对方后，再发送`FIN`报文给对方来表示同意现在`关闭连接`，因此，己方`ACK和FIN`一般都会分开发送。

## 为什么TIME_WAIT状态需要经过2MSL(最大报文段生存时间)才能返回到CLOSE状态？
原因有二：
一、保证TCP协议的全双工连接能够可靠关闭
二、保证这次连接的重复数据段从网络中消失

先说第一点，如果Client直接CLOSED了，那么由于IP协议的不可靠性或者是其它网络原因，导致Server没有收到Client最后回复的ACK。那么Server就会在超时之后继续发送FIN，此时由于Client已经CLOSED了，就找不到与重发的FIN对应的连接，最后Server就会收到RST而不是ACK，Server就会以为是连接错误把问题报告给高层。这样的情况虽然不会造成数据丢失，但是却导致TCP协议不符合可靠连接的要求。所以，Client不是直接进入CLOSED，而是要保持TIME_WAIT，当再次收到FIN的时候，能够保证对方收到ACK，最后正确的关闭连接。

再说第二点，如果Client直接CLOSED，然后又再向Server发起一个新连接，我们不能保证这个新连接与刚关闭的连接的端口号是不同的。也就是说有可能新连接和老连接的端口号是相同的。一般来说不会发生什么问题，但是还是有特殊情况出现：假设新连接和已经关闭的老连接端口号是一样的，如果前一次连接的某些数据仍然滞留在网络中，这些延迟数据在建立新连接之后才到达Server，由于新连接和老连接的端口号是一样的，又因为TCP协议判断不同连接的依据是socket pair，于是，TCP协议就认为那个延迟的数据是属于新连接的，这样就和真正的新连接的数据包发生混淆了。所以TCP连接还要在TIME_WAIT状态等待2倍MSL，这样可以保证本次连接的所有数据都从网络中消失。