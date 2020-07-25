### RabbitMQ实现RPC调用

---

#### 1、RPC调用结构图

![img](https://img-blog.csdn.net/20150930141503707?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 2、客户端

- 示例代码

  ```java
  ConnectionFactory factory = new ConnectionFactory();
  factory.setHost("127.0.0.1");
factory.setPort(5672);
  factory.setUsername("byron");
  factory.setPassword("byron");
  Connection connection = factory.newConnection();
  
  // new channel
  Channel channel = connection.createChannel();
  
  // new queue
  channel.queueDeclare("rpc.queue", false, false, false, null);
  String queueName = channel.queueDeclare("reply.to.queue", false, true, false, null).getQueue();
  
  // new exchange
  channel.exchangeDeclare("rpc.exchange", BuiltinExchangeType.DIRECT);
  
  // new binding
  channel.queueBind("rpc.queue", "rpc.exchange", "rpc");
  
  // publish new message
  String message = "hello";
  System.out.println("==> request: " + message);
  AMQP.BasicProperties basicProperties = new AMQP.BasicProperties.Builder().replyTo(queueName).build();
  channel.basicPublish("rpc.exchange", "rpc", basicProperties, message.getBytes());
  
  // get reply
  //        while(true){
  //            GetResponse response = channel.basicGet("reply.to.queue", true);
  //            if(response != null && response.getBody() != null){
  //                System.out.println("==> response: " + new String(response.getBody(), "UTF-8"));
  //            }
  //        }
  channel.basicConsume("reply.to.queue", true, new DefaultConsumer(channel){
      @Override
      public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
          System.out.println("==> response: " + new String(body, "UTF-8"));
      }
  });
  ```
  
- 两个队列

  - 回调队列，可以不绑定交换机
  - 请求队列，绑定一个交换机

#### 3、服务端

- 示例代码

  ```java
  ConnectionFactory factory = new ConnectionFactory();
  factory.setHost("127.0.0.1");
  factory.setPort(5672);
  factory.setUsername("byron");
  factory.setPassword("byron");
  Connection connection = factory.newConnection();
  
  // new channel
  Channel channel = connection.createChannel();
  
  // consume message and response
  channel.basicConsume("rpc.queue", true, new DefaultConsumer(channel){
      @Override
      public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
          String request = new String(body, "UTF-8");
          System.out.println("server receive request: " + request);
          
          String response = request + LocalDateTime.now().toString();
          channel.basicPublish("", properties.getReplyTo(), null, response.getBytes());
      }
  });
  ```

- 响应消息可以不指定交换机

