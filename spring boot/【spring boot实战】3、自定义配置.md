### 自定义配置

---

#### 1、影响自动配置的方式

- 显式配置进行覆盖
- 使用属性进行精细化配置

#### 2、覆盖Spring Boot自动配置

- 保护应用程序

  ![image-20200727224159842](https://i.loli.net/2020/07/27/POrmw9yY7kdIMlB.png)

- 自定义安全配置

  ![image-20200727224413916](https://i.loli.net/2020/07/27/rhjGtyUMQlzAEvB.png)

![image-20200727224446712](https://i.loli.net/2020/07/27/dV5YWpO7MmNsE3v.png)

通过以上的自定义配置类，我们让Spring Boot跳过了安全自动配置，转而使用我们的安全配置。

- 自动配置的神秘面纱
  - 自动配置使用了Spring4.0的条件化配置，可以在运行时判断这个配置该被运用还是该被忽略
  - @ConditionalOnMissingBean

#### 3、属性文件外置配置

- 命令行参数

  ```shell
  java -jar readinglist-0.0.1-snapshot.jar --spring.main.show-banner=false
  ```

- application.properties

  ```properties
  spring.main.show-banner=false
  ```

- application.yml

  ```yml
  spring:
  	main:
  		show-banner: false
  ```

- 设置环境变量

  ```shell
  export spring_main_show_banner=false
  ```

- 使用Profile进行配置

