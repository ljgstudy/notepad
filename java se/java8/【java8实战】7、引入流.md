### 引入流

---

#### 1、流简介

- 流是从支持数据处理操作的源生成的元素序列
  - 元素序列 - 就像集合一样，流也提供一个接口，可以访问特定元素类型
  - 源 - 流会使用一个提供数据的源，如集合、数组或者输入/输出资源。从有序集合生成流时会保留所有的顺序。由列表生成的流，其元素与列表一致
  - 数据处理操作 - 流的数据处理功能支持类似于数据库的操作，以及函数式编程语言中的常用操作。流的操作可以顺序执行也可以并行执行。

- 两个特点
  - 流水线
  - 内部迭代

- 处理流程

  ![image-20200728162706180](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728162706180.png)

#### 2、集合和流

- 集合 - 是一个内存的数据结构，它包含数据结构中目前所有的值 - 集合中的每个元素都得先计算出来才能添加到集合中

- 流 - 概念上固定的数据结构（不能添加或者删除元素），其元素则是按需计算的。从另一个角度讲，流就像是一个延迟创建的集合：只有在消费者要求的时候才会计算值（用管理学的话说这就是需求驱动，甚至是实时制造）

- 流只能消费一次

  ```java
  // java.lang.IllegalStateException:流已被操作或关闭
  List<String> title = Arrays.asList("Java8", "In", "Action");
  Stream<String> s = title.stream();
  s.forEach(System.out::println);
  s.forEach(System.out::println);
  ```

- 外部迭代

  ```java
  List<String> names = new ArrayList<>();
  for(Dish d: menu){
  	names.add(d.getName());
  }
  ```

- 内部迭代

  ```java
  List<String> names = menu.stream().map(Dish::getName).collect(toList());
  ```

- 外部迭代和内部迭代的区别

  ![image-20200728164259646](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728164259646.png)

#### 3、流的操作

![image-20200728164650656](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728164650656.png)

- 中间操作

  - filter，map，limit等
  - 等中间操作会返回另一个流。这让多个操作可以连接起来形成一个查询。重要的是，除非流水线上触发一个终端操作，否则中间操作不会执行任何处理——它们很懒。这是因为中间操作一般都可以合并起来，在终端操作时一次性全部处理

- 终端操作

  - collect
  - 终端操作会从流的流水线生成结果

- 具体使用

  - 一个数据源（如集合）来执行一个查询；
  - 一个中间操作链，形成一条流的流水线；
  -  一个终端操作，执行流水线，并能生成结果

- 中间操作和终端操作的示例

  ![image-20200728165637994](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728165637994.png)