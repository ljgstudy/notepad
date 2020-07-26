### RabbitMQ集群-搭建

---

#### 1、停止现有的RabbitMQ server

- 关闭现有单节点RabbitMQ server
- 移除（2.7.0之前）或禁用（2.7.0及之后）RabbitMQ插件
  - 因为像RabbitMQ Management插件会监听专门的端口号来提供服务，所以在第二个或者第三个节点启动了他们的插件，就会和第一个启动节点的插件冲突，然后节点就会崩溃了

#### 2、windows搭建集群的脚本

```bash
set RABBITMQ_NODE_PORT=6001
set RABBITMQ_NODENAME=rabbit1
set RABBITMQ_SERVICE_NAME=rabbit1
set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15672}]
call rabbitmq-server -detached
set RABBITMQ_NODE_PORT=6002
set RABBITMQ_NODENAME=rabbit2
set RABBITMQ_SERVICE_NAME=rabbit2
set RABBITMQ_SERVER_START_ARGS=-rabbitmq_management listener [{port,15673}]
call rabbitmq-server -detached
call rabbitmqctl -n rabbit2 stop_app
call rabbitmqctl -n rabbit2 join_cluster rabbit1@{hostname}
call rabbitmqctl -n rabbit2 start_app
call rabbitmqctl -n rabbit1 set_policy ha-all "^.*" "{""ha-mode"":""all""}"
```

![image-20200726224714048](https://i.loli.net/2020/07/26/BG2RP8InLc1g7yA.png)

![image-20200726224748214](https://i.loli.net/2020/07/26/RgeJ68nK2Q3CPvM.png)

#### 3、RabbitMQ集群模式

- clustering
- federation
- shovel

