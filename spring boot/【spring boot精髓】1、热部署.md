### 热部署

---

修改类时必须再次运行应用，对于开发者来讲非常不方便。Spring Boot提供了spring-boot-devtools, 它能在修改类或者配置文件的时候自动加载Spring Boot应用，需要打开pom文件，引入配置：

```xml
<dependency>
    <groupid>org.springframework.boot</groupid>
    <artifactid>spring-boot-devtools</artifactid>
    <optional>true</optional>
</dependency>
```