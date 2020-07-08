### 客户端开发向导-连接RabbitMQ

#### 1、Connection和Channel

- Connection可以创建多个Channel实例
- 应用程序应该为每个线程开辟一个Channel
- 多线程共享Channel实例是非线程安全的
  - 某些情况下Channel的操作可以并发运行，但是在其他情况下会导致在网络上出现错误的通信帧交错，同时也会影响发送方确认（publish confirm）机制的运行

#### 2、不推荐在生产环境上使用isOpen方法

- isOpen()方法依赖于shutdownCause的存在，由可能产生竞争
- 通常情况下，在调用createXXX或者newXXX方法之后，我们可以简单地认为Connection或者channel已经成功地处于开启状态，如果在使用channel的时候其已经处于关闭状态，那么程序会抛出一个ShutdownSignalException, 程序只需捕获这个异常即可。

#### 3、正确的示例代码

```java
try{
    ...
    channel.basicQos(1);
    ...
}catch(ShutdownSignalException sse){
    // possibly check if channel was closed
    // by the time we started action and reasons for closing it
    ...
}
```

#### 4、错误的示例代码

```java
if(channel.isOpen()){
    // the following code depends on the channel beding in open state.
    // However there is a possibility of the change in the channel state
    // between isOpen() and basicQos(1) call
    ...
    channel.basicQos(1);
}
```

#### 5、isOpen()方法的源码

```java
public boolean isOpen(){
    synchronized(this.monitor){
        return this.shutdownCause == null;
    }
}
```