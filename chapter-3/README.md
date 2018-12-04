# 第三章 Netty 的组件和设计

主要内容

- Netty 的技术和体系结构方面的内容
- Channel、EventLoop 和 ChannelFuture
- ChannelHandler 和 ChannelPipeline
- 引导

3.1 Channel、EventLoop 和 ChannelFuture

- Channel —— Socket
- EventLoop —— 控制流、多线程处理、并发
- ChannelFuture —— 异步通知

3.1.1 Channel 接口

基本的 I/O 操作（bind()、connect()、read() 和 write() ）依赖于底层网络传输所提供的原语。

在基于 Java 的网络编程中，其基本构造的是 class Socket。

Netty 的 Channel 接口所提供的 API，大大地降低了直接使用 Socket 类的复杂性。此外，Channel 也是拥有许多预定义的、专门化实现的广泛类层次结构的根，下面是一个简短的部分清单：

- EmbeddedChannel
- LocalServerChannel
- NioDatagramChannel
- NioSctpChannel
- NioSocketChannel

3.1.2 EventLoop 接口

3.1.3 ChannelFuture 接口

3.2 ChannelHandler 和 ChannelPipeline

3.2.1 ChannelHandler 接口

3.2.2 ChannelPipeline 接口

3.2.3 更加深入地了解 ChannelHandler

3.2.4 编码器和解码器

3.2.5 抽象类 SimpleChannelInboundHandler

3.3 引导

3.4 小结