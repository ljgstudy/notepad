### 入门

---

#### 1、RabbitMQ整体的模型架构

![image-20200708213827962](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200708213827962.png)

#### 2、几个概念

- Producer, 生产者也就是投递消息的一方
  - 消息体
  - 标签
    - 消息的标签来表述这条消息，比如一个交换器的名称和一个路由键。生产者把消息交由RabbitMQ, RabbitMQ之后会根据标签把消息发送给感兴趣的消费者
- Consumer: 消费者，就是接收消息的一方
- Broker，消息中间件的服务节点
- 队列，RabbitMQ的内部对象，用于存储消息
- 交换器（机），交换器可以将消息路由到一个或者多个队列中
- RoutingKey, 用来指定消息的路由规则，需要与交换器类型和绑定键联合使用才有效
- Binding, RabbitMQ中通过绑定将交换器与队列关联起来

#### 3、处理消息的流程

![image-20200708214111513](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200708214111513.png)

#### 4、交换器类型

- fanout， 把所有发送到该交换器的消息路由到所有与该交换器绑定的队列上
- direct, 把消息路由到那些BindingKey和RoutingKey完全匹配的队列上
- topic
  - RoutingKey为一个点号分割的字符串
  - BindingKey和RoutingKey一样也是点号分割的字符串
  - BindingKey中可以存在两种特殊字符串“*”和“#”，用于做模糊匹配，其中“#”用于匹配一个单词，“#”用于匹配多规格单词（可以是零个）
- headers, 不依赖路由键的匹配规则来路由消息，而是根据发送的消息内容中的headers属性来匹配。性能很差，不实用

