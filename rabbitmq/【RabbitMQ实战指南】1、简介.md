### 简介

---

#### 1、消息中间件的作用

- 解耦
- 冗余存储
- 拓展性
- 削峰
  - 应对流量洪峰，个人认为和缓冲说的是一个意思
- 可恢复性
  - 系统一部分组件失效后，不会影响整个系统。消息中间件降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入消息中间件的消息依然可以在系统恢复后进行处理
- 顺序性
  - 队列的基本属性，消息的有序性
- 缓冲
- 异步通信

#### 2、RabbitMQ的特点

- 可靠性
  - 持久化
  - 传输确认
  - 发布确认
- 灵活的路由
  - 内置交换机，交换机和队列的绑定关系可配置
- 拓展性
  - 多个RabbitMQ节点可以组成一个集群
- 高可用性
  - 队列可以在集群中的机器上设置镜像
- 多种协议
  - AMQP
  - STOMP
  - MQTT
- 多语言客户端
  - Java
  - Python
  - Ruby
  - PHP
  - C#
  - JavaScript
- 管理界面
- 插件机制

#### 3、示例代码

- 发送方

  ```java
  public class RabbitMQProducer {
  
      // constant
      private static final String EXCHANGE_NAME = "exchange_demo";
      private static final String ROUTING_KEY = "routing_key_demo";
      private static final String QUEUE_NAME = "queue_demo";
      private static final String IP_ADDRESS = "127.0.0.1";
      private static final Integer PORT = 5672;
      private static final String USER_NAME = "byron";
      private static final String PASSWORD = "byron";
  
      public static void main(String[] args) throws IOException, TimeoutException {
  
          // create new connection
          ConnectionFactory factory = new ConnectionFactory();
          factory.setHost(IP_ADDRESS);
          factory.setPort(PORT);
          factory.setUsername(USER_NAME);
          factory.setPassword(PASSWORD);
          Connection connection = factory.newConnection();
  
          // create channel
          Channel channel = connection.createChannel();
  
          // create direct exchange
          channel.exchangeDeclare(EXCHANGE_NAME, "direct", true, false, null);
  
          // create queue
          channel.queueDeclare(QUEUE_NAME, true, false, false, null);
  
          // bind exchange to queue
          channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, ROUTING_KEY);
  
          // send "hello world"
          String message = "hello world";
          channel.basicPublish(EXCHANGE_NAME, ROUTING_KEY,
                  MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes());
  
          // close channel
          channel.close();
          // create connection
          connection.close();
      }
  }
  ```

- 消费方

  ```java
  public class RabbitMQConsumer {
  
      private static final String QUEUE_NAME = "queue_demo";
      private static final String IP_ADDRESS = "127.0.0.1";
      private static final Integer PORT = 5672;
      private static final String USER_NAME = "byron";
      private static final String PASSWORD = "byron";
  
      public static void main(String[] args) throws IOException, TimeoutException, InterruptedException {
  
          // create connection
          Address[] address = new Address[]{
                  new Address(IP_ADDRESS, PORT)
          };
          ConnectionFactory connectionFactory = new ConnectionFactory();
          connectionFactory.setUsername(USER_NAME);
          connectionFactory.setPassword(PASSWORD);
          Connection connection = connectionFactory.newConnection(address);
  
          final Channel channel = connection.createChannel();
          channel.basicQos(64);
          Consumer consumer = new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  System.out.println("recv message: " + new String(body));
                  try{
                      TimeUnit.SECONDS.sleep(1);
                  }catch (InterruptedException e){
                      e.printStackTrace();
                  }
  
                  channel.basicAck(envelope.getDeliveryTag(), false);
              }
          };
          channel.basicConsume(QUEUE_NAME, consumer);
          TimeUnit.SECONDS.sleep(5);
  
          // create connection
          channel.close();
          connection.close();
      }
  }
  ```

#### 4、小插曲（剪不断理还乱的日志库）

- 报错如下

  ```
  SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
  SLF4J: Defaulting to no-operation (NOP) logger implementation
  SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
  ```

- 解决方案，pom.xml添加以下依赖

  ```
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-api</artifactId>
              <version>1.7.30</version>
          </dependency>
          <dependency>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
              <version>1.7.30</version>
          </dependency>
  ```

  