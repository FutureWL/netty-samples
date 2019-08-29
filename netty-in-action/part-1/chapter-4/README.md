# 第四章 传输

- 主要内容

- OIO —— 阻塞传输
- NIO —— 异步传输
- Local —— JVM 内部的异步通信
- Embedded —— 测试你的 ChannelHandler

4.1 案例研究：传输迁移

4.1.1 不通过 Netty 使用 OIO 和 NIO

4.1.2 通过 Netty 使用 OIO 和 NIO

4.1.3 非阻塞的 Netty 版本

4.2 传输 API

4.3 内置的传输

4.3.1 NIO —— 非阻塞 I/O

4.3.2 Epoll —— 用于 Linux 的本地非阻塞传输

4.3.3 OIO —— 旧的阻塞 I/O

4.3.4 用于 JVM 内部通信的 Local 传输

4.3.5 Embedded 传输

4.4 传输的用例

4.5 小结