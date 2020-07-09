### 发送消息

---

#### 1、发送消息，basicPublish参数介绍

```java
void basicPublish(String exchange, String routingKey, boolean mandatory, boolean immdiate, BasicProperties props, byte[] body) throws IOException;
```

- exchange: 交换器的名称，指明消息需要发送到哪个交换器中。如果设置为空字符串，则消息会被发送到RabbitMQ的默认交换器中
- routingKey: 路由键，交换器根据路由键将消息存储到相应的队列中
- props： 消息的属性集合。其中包含14个属性成员，分别有contentType、contentEncoding、headers(Map<String,  Object>)、deliverMode、priority、correlationId、replyTo、expiration、messageId、timestamp、type、userId、appId、clusterId
- byte[] body: 消息体（payload）,真正需要发送的消息

#### 2、消息属性集合的示例代码

- content-type、priority

  ```java
  channel.basicPublish(exchangeName, routingKey, new AMQP.BasicProperties.Builder().contentType("text/plain").deliverMode(2).priority(1).userId("hidden").build(), messageBodyBytes)
  ```

- 带有headers的消息

  ```java
  Map<String, Object> headers = new HashMap<String, Object>();
  headers.put("location", "here");
  headers.put("time", "today");
  
  channel.basicPublish(exchangeName, routingKey, new AMQP.BasicProperties.Builder().headers(headers).build(), messageBodyBytes);
  ```

- 带有过期时间的消息

  ```java
  channel.basicPublish(exchangeName, routingKey, new AMQP.BasicProperties.Builder().expiration("60000").build(), messageBodyBytes);
  ```

  

