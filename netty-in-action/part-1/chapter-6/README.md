# 第六章 ChannelHandler 和 ChannelPipeline

- 主要内容

- ChannelHandler API 和 ChannelPipeline API
- 检测资源泄露
- 异常处理

6.1 ChannelHandler 家族

6.1.1 Channel 的生命周期

1. ChannelUnregistered
2. ChannelRegistered
3. ChannelActive
4. ChannelInactive

6.1.2 ChannelHandler 的生命周期

1. handlerAdded
2. handlerRemoved
3. exceptionCaught

6.1.3 ChannelInboundHandler 接口

1. ChannelUnregistered
2. ChannelRegistered
3. ChannelActive
4. ChannelInactive
5. ChannelReadComplete
6. ChannelRead
7. channelWritabilityChanged
8. userEventTriggered


6.1.4 ChannelOutboundHandler 接口

出站操作和数据将由 ChannelOutboundHandler 处理。

类型|描述
---|---
bind(ChannelHandlerContext,SocketAddress,ChannelPromise)|-
connect(ChannelHandlerContext,SocketAddress,SocketAddress,ChannelPromise)|-
disconnect(ChannelHandlerContext,ChannelPromise)|-
close(ChannelHandlerContext,ChannelPromise)|-
deregister(ChannelHandlerContext,ChannelPromise)|-
read(ChannelHandlerContext)|-
flush(ChannelHandlerContext)|-
write(ChannelHandlerContext,Object,ChannelPromise)|-

6.1.5 ChannelHandler 适配器

6.1.6 资源管理

6.2 ChannelPipeline 接口

ChannelHandlerContext 使得 ChannelHandler 能够和它的 ChannelPipeline 以及其他的 ChannelHandler 交互。

6.2.1 修改 ChannelPipeline

6.2.2 触发事件

6.3 ChannelHandlerContext 接口

6.3.1 使用 ChannelHandlerContext 接口

6.3.2 ChannelHandler 和 ChannelHandlerContext 的高级用法

6.4 异常处理

6.4.1 处理入站异常

6.4.2 处理出站异常

6.5 小结


