### 滴滴社招面试题：为什么用Rabbit MQ

---

#### 1、本质上解决的是通信的问题

- 可以实现异步架构

- 可以实现系统系统解耦

- 削峰

#### 2、为什么使用AMQP

- JMS: Java Message Service,
- AMQP

#### 3、RabbitMQ工作机制

![image-20200705213233722](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200705213233722.png)

- 发送消息和接收消息采用TCP长连接，channel复用长连接
- 消息发送后存储在Queue里面，独立运行的进程，有独立的数据库（Mneisa）
- 消息接收者监听Queue
- Exchange和Queue是多对多的关系，Exchange可以理解为地址清单，决定将消息路由到哪个Queue里面去
- VHost虚拟主机，类型namespace或者package的意图

#### 4、Exchange交换机

- 类型
  - Direct，直连类型
    - ![image-20200705213859885](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200705213859885.png)
    - binding key, 发送的消息的routing key发送到和binding key相同的queue中
  - Topic，主题类型
    - ![image-20200705214045276](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200705214045276.png)
    - #表示0个或者多个单词，*表示1个单词
  - Fanout, 广播类型的交换机
    - ![image-20200705214400994](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200705214400994.png)
    - 不需要任何的绑定键，和此交换机绑定的queue都会收到消息



#### 5、spring boot集成Rabbit MQ

- 线上环境走什么样的流程拿到想要的MQ资源
  - ![image-20200705214955202](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200705214955202.png)
- 为什么继承到spring中？
  - 方便的管理Bean
  - Template简化了方法的调用
- 为什么集成到spring boot中？
  - 自动化配置
- 怎么整合？
  - 配置类，创建交换机，创建队列，创建绑定关系
  - 消费类，监听类（监听队列）
  - 生产者，调用template发送消息（Exchange）
  - 服务器信息配置
- 怎么保证可靠
  - Transaction confirm
  - 持久化
  - ACK, 手动和自动
  - 死信队列