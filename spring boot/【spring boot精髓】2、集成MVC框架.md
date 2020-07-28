### 集成MVC框架

---

#### 1、MVC框架会处理类似如下相同的技术需求

- HTTP URL映射到Controller某个方法
- HTTP参数映射到Controller方法的参数上，比如参数映射到某个Java对象，或者上传附件映射到某个File对象上
- 参数的校验
- MVC错误处理
- MVC中如何调用视图
- MVC中如何序列化对象成JSON
- 拦截器等高级定制

　#### 2、集成MVC框架

```xml
<depeηdency>
	<groupid>org.springframework.boot</groupid>
	<artifactid>spring-boot-starter-web</artifactid>
</dependency>
```

#### 3、使用controller

- 不需要集成某个基础类才能处理用户的HTTP请求
- 只需要在类上声明@Controller注解
- 使用@Controller的注解类下，接口返回的时视图名，如果想直接返回内容而不是视图名，则需要在方法上使用@ResponseBody
- @ResponeBody注解直接将返回的对象输出到客户端，如果时字符串，则直接返回；如果不是，则默认使用Jackson序列化成JSON字符串输出

#### 4、URL映射到方法

- @Request Mapping

  - value, 请求URL的路径，支持URL模板、正则表达式
  - method，HTTP请求方法，有GET、POST、PUT等
  - consumes, 允许的媒体类型，如consumes="application/json",对应于请求的HTTP的Content-Type
  - produces, 相应的媒体类型，如produces=“application/json”,对应于HTTP的Accept字段
  - params, 请求的参数，如果params="action=update"
  - headers, 请求的HTTP头的值

- URL路径匹配

  ```java
  @RequestMapping(value ＝ "／get／{id}.json”)
  	public @ResponseBody User getByid ( @PathVariable (”id”) Long id) {
  	return userService . getUserByid (id) ;
  }
  ```

  如上面的例子所示 ， 访问路径是/get/1.json，将调用 getByld 方法，且参数 id 的值是 1 。 注
  解 PathVariable 作用在方法参数上，用来表示参数的值来自于 URL 路径 。  

- HTTP method匹配

  - 提供method属性来映射对应HTTP的请求方法，通常HTTP的请求方法有如下内容：
    - GET ，用来获取 URL 对应的内容 。
    - POST ， 用来向服务器提交信息。
    - HEAD ，同 GET ，但不返回消息体，通常用于返回 URL 对应的元信息，如过期时间等 
    - 搜索引擎通常用 HEAD 来获取网页信息。
    - PUT ， 同 POST ，用来向服务器提交信息，但语义上更像一个更新操作 。同一个数据 ，
      多次 PUT 操作 ，也不会导致数据发生改变。 而 POST 在语义上更类似新增操作 。
    - DELETE，删除对应 的资源信息。
      PATCH 方法 ， 类似 PUT 方法 ， 表示信息 的局部更新。  

#### 5、方法参数

- pathVariable - 请求的URL中获取参数并映射到方法参数中
- Model&ModelAndView
- JavaBean
- @RequestBody
- MutipartFile
- @ModelAttribute
- @InitBinder

#### 6、验证框架

#### 7、WebMVCConfigurer

#### 8、视图技术

#### 9、通用错误处理

#### 10、Service和Transactional

#### 11、curl