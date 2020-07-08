#### AMQP协议介绍

---

#### 1、AMQP协议的组成

- Module Layer: 位于协议最高层，主要定义了一些供客户端调用的指令，客户端可以利用这些命令实现自己的业务逻辑。例如，客户端可以使用Queue.Declare命令声明一个队列或者使用Basic.Consume订阅消费一个队列中的消息
- Session Layer: 位于中间层，主要负责将客户端的命令发送到服务器，再将服务器的应答返回给客户端，主要为客户端和服务器之间的通信提供可靠性同步机制和错误处理
- Transport Layer: 位于最底层，主要传输二进制数据流，提供帧的处理、信道复用、错误检测和数据表示等。

#### 2、AMQP生产者流转过程

![image-20200708221121210](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200708221121210.png)

#### 3、AMQP消费者流转过程

![image-20200708221204297](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200708221204297.png)

