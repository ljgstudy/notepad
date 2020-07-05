### Spring IOC 概述

---

#### 1、spring ioc怎么工作的

![container magic](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/images/container-magic.png)



#### 2、配置元信息

- XML configuration

  - <bean></bean>

- Annotation-based configuration

  - <context:annotation-config />

- Java-based configuration

  - @Configuration, @Bean, @Import, @DependOn

    

#### 3、示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person" class="org.geekbang.practice.in.spring.ioc.Person" />

</beans>
```



```java
public class Person {
    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



```java
public class SimpleSpringIOCDemo {
    public static void main(String[] args) {

        // 初始化应用上下文
        ApplicationContext context = new ClassPathXmlApplicationContext("services.xml");

        // 获取bean
        context.getBean("person");
        Person person = context.getBean("person", Person.class);
        System.out.println(person);
    }
}
```

