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

1.2.2 异步和事件驱动

1.3 Netty 的核心组件

1.3.1 Channel

1.3.2 回调

1.3.3 Future

1.3.4 事件和 ChannelHandler

1.3.5 把它们放在一起

1.4 小结