### spring boot入门

---

#### 1、Spring Boot精要

- 自动配置
  - 针对很多Spring应用程序常见的应用功能，SpringBoot能自动提供相关配置
- 起步依赖
  - 告诉Spring Boot需要什么功能，它就能引入需要的库
- 命令行界面
  - 这是Spring Boot可选的特性，借此你需要写代码就能完成完成的应用程序，无需传统项目的构建
- Actuator
  - 提供在运行时检视应用程序内部情况的能力，比如
    - spring应用程序上下文配置的bean
    - Spring Boot的自动配置做的决策
    - 应用程序去到的环境变量、系统属性、配置属性和命令行参数
    - 应用程序里线程的当前状态
    - 应用程序最近处理过的HTTP请求的追踪情况
    - 各种和内存用量、垃圾回收、web请求以及数据源用量相关指标

#### 2、Spring Boot误区

- **Spring Boot不是应用服务器**。Spring Boot在应用程序中嵌入了一个Servlet容器（Tomca、Jetty或Undertow）以此实现这一功能。但这是内嵌的servlet容器提供的功能，不是Spring Boot实现的。
- **Spring Boot没有实现诸如JPA或JMS之类的企业级java规范**。例如，Spring Boot没有实现JPA，不过它自动装配了某个JPA实现（比如Hibernate）的Bean，以此支持JPA
- **Spring Boot没有引入任何的代码生成**。只是利用了Spring 4的条件化配置特性，以及maven和gradle提供的传递依赖解析，以此实现Spring应用程序上下文里的自动配置

#### 3、Spring Initializer的使用方法

- 通过Web界面使用
- 通过Spring Tool Suite使用
- 通过IntelliJ IDEA使用
- 使用Spring Boot CLI使用