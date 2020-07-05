### bean概述

---

#### 1、bean definitions 包含一下几个信息

- 类的完全限定名
- scope, lifecycle callbacks, and so forth
- 依赖。References to other beans that are needed for the bean to do its work. These references are also called collaborators or dependencies.
- Other configuration settings to set in the newly created object — for example, the size limit of the pool or the number of connections to use in a bean that manages a connection pool.

| Property                 | Explained in…                                                |
| :----------------------- | :----------------------------------------------------------- |
| Class                    | [Instantiating Beans](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-class) |
| Name                     | [Naming Beans](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-beanname) |
| Scope                    | [Bean Scopes](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-scopes) |
| Constructor arguments    | [Dependency Injection](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-collaborators) |
| Properties               | [Dependency Injection](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-collaborators) |
| Autowiring mode          | [Autowiring Collaborators](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-autowire) |
| Lazy initialization mode | [Lazy-initialized Beans](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-lazy-init) |
| Initialization method    | [Initialization Callbacks](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-lifecycle-initializingbean) |
| Destruction method       | [Destruction Callbacks](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-lifecycle-disposablebean) |



#### 2、 命名bean

- id，唯一指定

- name，可以指定多个

- id，name并不是一定要指定，如果么有指定id，spring 会自动生成一个id。如果想在其他bean中引用此bean则需要指定一个id或者name

- bean命名采用小驼峰，比如accountManager`, `accountService`, `userDao`, `loginController

- 别名，复用现有的BeanDefinition, 场景化的需求

  ```xml
  <alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
  <alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
  ```



#### 3、初始化bean

- 构造器初始化

  ```xml
  <bean id="exampleBean" class="examples.ExampleBean"/>
  <bean name="anotherExample" class="examples.ExampleBeanTwo"/>
  ```

- 静态工厂方法

  ```xml
  <bean id="clientService" class="examples.ClientService" 
        factory-method="createInstance"/>
  ```

- 实例工厂方法

  ```xml
  <bean id="clientService"
      factory-bean="serviceLocator"
      factory-method="createClientServiceInstance"/>
  ```

- ##### Determining a Bean’s Runtime Type

