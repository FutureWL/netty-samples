# 第十章 编解码器框架

主要内容

- 解码器、编码器以及编解码器的概述
- Netty 的编解码器类

10.1 什么是编解码器

10.2 解码器

10.2.1 抽象类 ByteToMessageDecoder

10.2.2 抽象类 ReplayingDecoder

10.2.3 抽象类 MessageToMessageDecoder

10.2.4 TooLongFrameException 类

10.3 编码器

10.3.1 抽象类 MessageToByteEncoder

10.3.2 抽象类 MessageToMessageEncoder

10.4 抽象的编解码器类

10.4.1 抽象类 ByteToMessageCodec

10.4.2 抽象类 MessageToMessageCodec

10.4.3 CombinedChannelDuplexHandler 类

10.5 小结

