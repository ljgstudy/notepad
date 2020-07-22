### 客户端消息预加载

---

#### 1、客户端消息是否存在预加载？

是

#### 2、客户端消息预加载的作用

提高效率

#### 3、客户端预加载配置

```java
basic.qos(1)
```

#### 4、官方文档说明

```markdown
Because messages are sent (pushed) to clients asynchronously, there is usually more than one message "in flight" on a channel at any given moment. In addition, manual acknowledgements from clients are also inherently asynchronous in nature. So there's a sliding window of delivery tags that are unacknowledged. Developers would often prefer to cap the size of this window to avoid the unbounded buffer problem on the consumer end. This is done by setting a "prefetch count" value using the basic.qos method. The value defines the max number of unacknowledged deliveries that are permitted on a channel. Once the number reaches the configured count, RabbitMQ will stop delivering more messages on the channel unless at least one of the outstanding ones is acknowledged.

For example, given that there are delivery tags 5, 6, 7, and 8 unacknowledged on channel Ch and channel Ch's prefetch count is set to 4, RabbitMQ will not push any more deliveries on Ch unless at least one of the outstanding deliveries is acknowledged. When an acknowledgement frame arrives on that channel with delivery_tag set to 5 (or 6, 7, or 8), RabbitMQ will notice and deliver one more message. Acknowledging multiple messages at once will make more than one message available for delivery.

It's worth reiterating that the flow of deliveries and manual client acknowledgements is entirely asynchronous. Therefore if the prefetch value is changed while there already are deliveries in flight, a natural race condition arises and there can temporarily be more than prefetch count unacknowledged messages on a channel.

Per-channel, Per-consumer and Global Prefetch
The QoS setting can be configured for a specific channel or a specific consumer. The Consumer Prefetch guide explains the effects of this scoping.

Prefetch and Polling Consumers
The QoS prefetch setting has no effect on messages fetched using the basic.get ("pull API"), even in manual confirmation mode.
```

#### 5、确认模式，预加载以及吞吐量的描述

```markdown
Acknowledgement mode and QoS prefetch value have significant effect on consumer throughput. In general, increasing prefetch will improve the rate of message delivery to consumers. Automatic acknowledgement mode yields best possible rate of delivery. However, in both cases the number of delivered but not-yet-processed messages will also increase, thus increasing consumer RAM consumption.

Automatic acknowledgement mode or manual acknowledgement mode with unlimited prefetch should be used with care. Consumers that consume a lot of messages without acknowledging will lead to memory consumption growth on the node they are connected to. Finding a suitable prefetch value is a matter of trial and error and will vary from workload to workload. Values in the 100 through 300 range usually offer optimal throughput and do not run significant risk of overwhelming consumers. Higher values often run into the law of diminishing returns.

Prefetch value of 1 is the most conservative. It will significantly reduce throughput, in particular in environments where consumer connection latency is high. For many applications, a higher value would be appropriate and optimal.

When Consumers Fail or Lose Connection: Automatic Requeueing
When manual acknowledgements are used, any delivery (message) that was not acked is automatically requeued when the channel (or connection) on which the delivery happened is closed. This includes TCP connection loss by clients, consumer application (process) failures, and channel-level protocol exceptions (covered below).

Note that it takes a period of time to detect an unavailable client.

Due to this behavior, consumers must be prepared to handle redeliveries and otherwise be implemented with idempotence in mind. Redeliveries will have a special boolean property, redeliver, set to true by RabbitMQ. For first time deliveries it will be set to false. Note that a consumer can receive a message that was previously delivered to another consumer.
```

