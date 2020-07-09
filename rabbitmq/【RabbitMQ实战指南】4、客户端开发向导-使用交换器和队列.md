### 使用交换器和队列

---

#### 1、exchangeDeclare方法详解

```java
Exchange.DeclareOk exchangeDeclare(String exchange, String type, boolean durable, boolean autoDelete, boolean internal, Map<String, Object> arguments) throws IOException;
```

- exchange: J交换机的名字
- type：交换机的类型，常见的如fanout， direct，topic
- durable，设置是否持久化。durable设置为true表示持久化，反之是非持久化。持久化可以将交换器存盘，在服务器重启的时候不会丢失相关信息。
- autoDelete: 设置是否自动删除。autoDelete设置为true则表示自动删除。自动删除的前提是至少由一个队列或者交换器与这个交换器绑定，之后所有与这个交换器绑定的队列或者交换器都与此解绑。注意不能错误地把这个参数理解为：当与此交换器连接的客户端都断开时，RabbitMQ会自动删除本交换器。
- internal：设置是否是内置的。如果设置为true，则表示是内置的交换器，客户端程序无法直接发送消息到这个交换器中，只是通过交换器路由到交换器的这种方式。
- argument：其他的一些结构化参数，比如alternative-exchange

#### 2、queueDeclare方法详解

```java
Queue.DeclareOk queueDeclare(String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments) throws IOException;
```

- queue:  队列名称
- durable：设置是否持久化。为true则设置队列持久化。持久化的队列会存盘，在服务器重启的时候可以保证消息不丢失。
- exclusive：设置是否排他。为true则设置队列为排他的。如果一个队列被声明为排他队列，则该队列对首次生声明它的连接可见，并在连接断开的时候自动删除。这里需要注意三点
  - 排他队列基于连接可见的，同一个连接的不同信道是可以同时访问同一个连接创建的排他队列
  - 如果一个连接已经声明了一个排他队列，其他的连接是不允许建立同名的排他队列的，这个与普通队列不同
  - 即使该队列是持久化的，一旦连接关闭或者客户端推出，该排他队列都会自动删除的，这种队列适用于一个客户端同时发送和读取消息的场景
- autoDelete: 设置是否自动删除。为true则设置队列为自动删除。自动删除的前提是：至少有一个消费者连接到这个队列，之后所有与这个队列连接的消费者都断开时，才会自动删除。不能把这个错误的理解为：当连接到此队列的所有客户端断开时，这个队列会自动删除，因为生产者客户端创建这个队列，或者没有消费者与这个队列连接时，都不会自动删除这个队列。
- arguments：设置队列的其他一些参数

#### 3、queueBind方法详解

```java
Queue.BindOk queueBind(String queue, String exchange, String routingKey, Map<String,Object> arguments) throws IOException;
```

- queue：队列名称
- exchange：交换机名称
- routingKey: 用来绑定队列和交换机的路由键
- argument：定义绑定的一些参数

#### 4、exchangeBind方法详解

我们不仅可以将交换器与队列绑定，也可以将交换器与交换器绑定，后者和前者的用法如出一辙。

生产者发送消息至交换器source中，交换器source根据路由键找到与其匹配的另一个交换器destination，并把消息转发到destination中，进而存储在destination绑定的队列queue中，可以参考下图

![image-20200709225514341](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200709225514341.png)

#### 5、何时创建

- 按照RabbitMQ官方建议，生产者和消费者都应该尝试创建（这里指声明操作）队列。
- 预先创建好资源还有一个好处时，可以确保交换器和队列之间正确地绑定匹配。
- 至于时使用预先分配创建资源的静态方式还是动态的创建方式，需要从业务逻辑本身，公司运维体系和公司硬件资源等方面考虑

