# 第一章 Netty —— 异步和事件驱动

本章主要内容

- Java 网络编程
- Netty 简介
- Netty 的核心组件

1.1 Java 网络编程


```java
// ====================
//     阻塞IO 示例
// ====================
// 1. 创建一个新的 ServerSocket，用以监听指定端口上的连接请求
ServerSocket serverSocket = new ServerSocket(portNumber);
// 2. 对 accept()方法的调用将被阻塞，直到一个连接建立
Socket clientSocket = serverSocket.accept();
// 3. 这些流对象，都派生于套接字的流对象
BufferedReader in = new BufferedReader(new InputStreamReader(clientSocker.getInputStream()));
PrintWriter out = new PrintWriter(ClientSocket.getOutputStream(),true);
String request,response;
// 4. 开始循环处理
while((request=in.readLine())!=null){
    // 5. 如果客户端发送了 "Done"，则退出处理循环
    if("Done".equals(request)){
        break;
    }
    // 6. 请求被传递给服务器的处理方法
    response = processRequest(request);
    // 7. 服务器的响应
    out.println(response);
    // 8. 继续执行处理循环
}
```

这段代码片段将只能同时处理一个连接，要管理多个并发客户端，需要为每个新的客户端 Socket 创建一个新的 Thread。

让我们考虑一下这种方案的影响。

第一，在任何时候都有可能有大量的线程处于休眠状态，只是等待输入或者输出数据就绪，这可能算是一种资源浪费。

第二，需要为每个线程的调用栈都分配内存，其默认值大小区间为 64KB 到 1MB ，具体取决于操作系统。

第三，即使 Java 虚拟机 （JVM）在物理上可以支持非常大数量的线程，但是远在到达该极限之前，上下文切换所带来的开销就会带来麻烦，例如，在达到 10 000 个连接的时候。

虽然这种并发方案对于支撑中小数量的客户端来说还算可以接受，但是为了支撑100 000 或者更多的并发连接所需要的资源使得它很不理想。幸运的是，还有一种方案。

1.1.1 Java NIO

- 可以使用 setsocket() 方法配置套接字，以便读写调用在没有数据的时候立即返回，也就是说，如果是一个阻塞调用应该已经被阻塞了；
- 可以使用操作系统的事件通知 API 注册一组非阻塞套接字，以确定它们中是否有任何的套接字已经有数据可供读写。

1.1.2 选择器

- 使用较少的线程便可以处理许多连接，因此也减少了内存管理和上下文切换所带来的开销；
- 当没有 I/O 操作需要处理的时候，线程也可以被利用于其他任务。

1.2 Netty 简介

表 1-1 Netty 的特性总结

分类|Netty 的特性
---|---
设计|统一的 API，支持多种传输类型，阻塞的和非阻塞的 <br/> 简单而强大的线程模型 <br/> 真正的无连接数据报套接字支持 <br/> 连接逻辑组件以支持复用
易于使用|详实的 Javadoc 和大量的实例集 <br/> 不需要超过 JDK 1.6+ 的依赖。（一些可选的特性可能需要 Java 1.7+ 和/或额外的支持） 
性能|拥有比 Java 的核心 API 更高的吞吐量以及更低的延迟 <br/> 得益于池化和复用，拥有更低的资源消耗 <br/> 最少内存复制
健壮性|不会因为慢速、快速或者超载的连接而导致 OutOfMemoryError <br/>消除在高速网络中 NIO 应用程序常见的不公平读、写比率
安全性|完整的 SSL/TLS 以及 StartTLS 支持 <br/> 可用于受限环境下，如 Applet 和 OSGI
社区驱动|发布快速而且频繁 

1.2.1 谁在使用 Netty

公司：Apple、Twitter、Facebook、Google、Square 和 Instagram

开源项目：Infinispan、HornetQ、Vert.x、Apache Cassandra 和 Elasticsearch

1.2.2 异步和事件驱动

**可伸缩性** 定义为：一种系统、网络或者进程在需要处理的工作不断增长时，可以通过某种可行的方式或者扩大它的处理能力适应这种增长的能力。

异步和可伸缩性之间的联系又是什么呢？

- 非阻塞网络调用使得我们可以不必等待一个操作的完成。完全异步的 I/O 正是基于这个特性构建的，并且更进一步：异步方法会立即返回，并且在它完成时，会直接或者在稍后的某个时间点通知用户。
- 选择器使得我们能够通过较少的线程便可监视许多连接上的事件。

1.3 Netty 的核心组件

- Channel
- 回调
- Future
- 事件 和 ChannelHandler

这些构建代表了不同类型的构造：资源、逻辑以及通知。你的应用程序将使用它们来访问网络以及流经网络的数据。

对于每个组件来说，我们都将提供一个基本定义，并且在适当的情况下，还会提供一个简单的示例代码来说明它的用法。

1.3.1 Channel

Channel 是 Java NIO 的一个基本构造。

> 它代表一个到实体（如一个硬件设备、一个文件、一个网络套接字或者一个能够执行一个或者多个不同的 I/O 操作的程序组件）的开放连接，如读操作和写操作。

目前，可以把 Channel 看作是传入（入站）或者传出（出站）数据的载体。因此，它可以被打开或者被关闭，连接或者断开连接。

1.3.2 回调

一个回调其实就是一个方法，一个指向已经被提供给另外一个方法的方法的引用。这使得后者可以在适当的时候调用前者。回调在广泛编程场景中都有应用，而且也是在操作完成后通知相关方最常见的方式之一。

Netty 在内部使用了回调来处理事件；当一个回调被触发时，相关的事件可以被一个 interfaceChannelHandler 来实现处理。

```java
// 被回调触发的 ChannelHandler
public class ConnectHandler extends ChannelInboundHandlerAdapter{    
    @Override
    public void channelActive(ChannelHandlerContext ctx){
        System.out.println("Client "+ctx.chanel().remoteAddress()+" connected");
    }
}
```

1.3.3 Future

Future 提供了另一种在操作完成时通知应用程序的方式。这个对象可以看作是一个异步操作的结果的占位符；它将在未来的某个时刻完成，并提供对其结果的访问。

1.3.4 事件 和 ChannelHandler

Netty 使用不同的事件来通知我们状态的改变或者是操作的状态。这使得我们能构基于已经发生的事件来触发适当的动作。这些动作可能是：

- 记录日志
- 数据转换
- 流控制
- 应用程序逻辑

Netty 是一个网络编程框架，所以事件是按照它们的入站或者出站数据流的相关性进行分类的。

可能由入站数据或者相关的状态更改而触发的事件包括：

- 连接已被激活或者连接失活
- 数据读取
- 用户事件
- 错误事件

出站事件是未来将会触发的某个动作的操作结果，这些动作包括

- 打开或者关闭到远程节点的连接
- 将数据写到或者冲刷到套接字

1.3.5 把它们放在一起

1 Future、回调 和 ChannelHandler

2 选择器、事件 和 EventLoop

Netty 通过触发事件 将 Selector 从 应用程序中抽象出来，消除了所有本来将需要手动编写的派发代码。

在内部，将会为每个 Channel 分配一个 EventLoop，用以处理所有事件，包括：

- 注册感兴趣的事件
- 将事件派发给 ChannelHandler
- 安排进一步的动作

EventLoop 本身只由一个线程驱动，其处理了一个 Channel 的所有 I/O 事件，并且在该 EventLoop 的整个生命周期内部都不会改变。

整个简单而强大的设计消除了你可能有的在 ChannelHandler 实现中需要进行同步的任何顾虑，因此，你可以专注于提供正确的逻辑，用来在有感兴趣的数据要处理的时候执行。
如同我们在详细探讨 Netty 的线程模型时将会看到的，该 API 时简单而紧凑的。

1.4 小结