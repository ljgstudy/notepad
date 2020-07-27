### 第一个Spring Boot程序

---

#### 1、Hello World程序解析

![image-20200727214826800](https://i.loli.net/2020/07/27/K3ak5veycrBf2jE.png)

@SpringBootApplication开启了Spring的组件扫描和Spring Boot的自动配置功能。实际上，@SpringBootApplication将三个有用的注解组合了一起。

- Spring的@Configuration：标明该类使用Spring基于Java的配置。倾向于使用基于Java而不是XML的配置
- Spring的@ConponentScan: 启用组件扫描，这样你写的Web控制器类和其他组件才能被自动发现并注册为Spring应用程序上下文中的Bean
- Spring Boot的@EnableAutoConfiguration: 开启Spring Boot自动配置的能力

在spring boot的早期版本中，你需要在类上同时标注这三个注解，但是从Spring Boot1.2.0开始，有@SpringBootApplication就行了。

#### 2、起步依赖

Spring Boot通过提供众多起步依赖降低项目依赖的复杂度。起步依赖本质上是一个Maven项目对象模型，定义了对其他库的传递依赖，这些东西加在一起即可支持某项功能。很多起步依赖的命名都暗示了它们提供的某种或者某类功能。

- 需要提供具体某个依赖的版本号。起步依赖本身的版本是由正在使用的Spring Boot的版本来决定的，而起步依赖则会决定它们引入的传递依赖的版本
- Spring Boot经过了足够的尝试，确保引入的全部依赖都能相互兼容
- 覆盖Spring Boot起步依赖引入的依赖时都要多加小心，虽然不同的版本放在一起也许没什么问题，但你要知道，起步依赖中的各个依赖版本之间的兼容性都经过了精心的测试。应该只在特殊情况下覆盖这些传递依赖

#### 4、使用自动配置

Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程，考虑了众多因素，才决定Spring配置应该用哪个，不该用哪个，比如

- Spring的JdbcTemplate是不是在classpath中，如果是，并且有DataSource的Bean，则自动配置一个JdbcTemplate的Bean
- Thymeleaf是不是自在classpath中，如果是，则配置Thymeleaf的模板解析器、视图解析器以及模板引擎
- Spring Security是不是在classpath中，如果是，则进行一个非常基本的Web安全设置

每当应用程序启动时，Spring Boot的自动配置都要做将近200个这样的决定，涵盖安全、继承、持久化、web开发等诸多方面。所有这些自动配置就是为了尽量不让你自己写配置。