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

1.1.1 Java NIO

1.2 Netty 简介

1.2.1 谁在使用 Netty

1.2.2 异步和事件驱动

1.3 Netty 的核心组件

1.3.1 Channel

1.3.2 回调

1.3.3 Future

1.3.4 事件和 ChannelHandler

1.3.5 把它们放在一起

1.4 小结