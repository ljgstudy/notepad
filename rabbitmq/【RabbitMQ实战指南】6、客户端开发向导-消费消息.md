### 消费消息

----

***RabbitMQ的消费模式分为两种：推（Push）和拉（pull）模式。推模式采用Basic.Consume()进行消费，而拉模式则是调用Basic.Get进行消费***

#### 1、推模式

- 在推模式下，可以通过持续订阅的方式来消费消息

- 接收消息一般通过实现Consumer接口或者继承DefaultConsumer类来实现

- 不同的订阅采用不同的消费者标签（consumerTag）来区分彼此，在同一个channel中的消费者也需要通过谓一的消费者标签以作区分

- 示例代码

  ```java
  boolean autoAck = false;
  channel.basicQos(64);
  channel.basicConsume(queueName, autoAck, "myConsumerTag", 
                      new DefaultConsumer(channel){
                          @Override
                          public void handleDelivery(String consumerTag,
                                                    Envelope envelope,
                                                    AMQP.BasicProperties properties,
                                                    byte[] body)
                              throws IOException{
                              String routingKey = envelope.getRoutingKey();
                              String contentType = properties.getContentType();
                              long delivery = envelope.getDeliveryTag();
                              
                              channel.basicAck(delivery, false);
                          }
                      })
  ```

  - 显式设置autoAck为false，然后在接收消息之后进行显式ack操作（channel.backAck）,对于消费者来说这个设置是非常必要的，可以防止消息不必要地丢失。

- String basicConsume(xxx)的参数介绍

  - queue: 队列名称
  - autoAck: 设置是否自动确认。建议设置为false，即不自动确认
  - consumerTag: 消费者标签，用来区分多个消费者
  - noLocal: 设置为true则表示不能将同一个Connection中生产者发送的消息传送给这个Connection中的消费者；
  - exclusive: 设置是否排他
  - argument: 设置消费者的其他参数
  - callBack: 设置消费者的回调函数。用来处理RabbitMQ推送过来的消息，比如DefaultConsumer,使用时需要客户端重写其中的方法
    - handleDeliver: 消费消息
    - handleShutdownSignal: 当channel或者connection关闭的时候会调用
    - handleConsumeOk: 会在其他方法之前调用，返回消费者标签
    - 重写handleCancelOk和handleCancel方法，这样消费端可以在显式或者隐式地取消订阅的时候调用。也可以通过channel.basicCancel方法显式地取消一个消费者订阅
    - 消费者客户端的这些callback会被分配到与channel不同的线程池上
    - 每个Channel都拥有自己独立的线程。最常用的做法时一个Channel对应一个消费者，也就是意味着消费者彼此之间么有任何关联。

#### 2、拉模式

- 示例图

  ![image-20200710073352670](C:\Users\byron\AppData\Roaming\Typora\typora-user-images\image-20200710073352670.png)

- 注意要点

  ```
  Basic.Consume将信道置为接收模式，直到取消队列的订阅为止。在接收模式期间，RabbitMQ会不断地推送消息给消费者，当推送消息的个数还是会收到Basic.Qos的限制。如果只想从队列中获得单条消息而不是持续订阅，建议还是使用Basic.Get进行消费。但是不能将Basic.Get放在一个循环来代替Basic.Consume,这样会严重影响RabbitMQ的性能。如果要实现高吞吐量，消费者理应使用Basic.Consume方法。
  ```

  