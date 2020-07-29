### 使用流（筛选和映射）

---

#### 1、筛选和切片

- 谓词筛选，filter

  ```java
  List<Dish> vegetarianMenu = menu.stream()
      .filter(Dish::isVegetarian)
  	.collect(toList());
  ```

  ![image-20200729084751792](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729084751792.png)

- 筛选各异元素，distinct

  ```java
  List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
  numbers.stream()
  	.filter(i -> i % 2 == 0)
  	.distinct()
  	.forEach(System.out::println);
  ```

  ![image-20200729084834989](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729084834989.png)

- 截断流， limit

  ```java
  List<Dish> dishes = menu.stream()
  	.filter(d -> d.getCalories() > 300)
  	.limit(3)
  	.collect(toList());
  ```

  ![image-20200729085020302](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729085020302.png)

- 跳过元素

  ```java
  List<Dish> dishes = menu.stream()
  	.filter(d -> d.getCalories() > 300)
  	.skip(2)
  	.collect(toList());
  ```

  ![image-20200729085108549](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729085108549.png)

#### 2、映射

- 对流中的每个元素应用函数

  ```java
  Student s1 = new Student();
  s1.setName("byron");
  s1.setAge(10);
  
  Student s2 = new Student();
  s2.setName("johnny");
  s2.setAge(10);
  
  List<Student> studentList = Arrays.asList(s1, s2);
  
  // map
  // 收集所有学生的姓名
  List<String> studentNames = studentList.stream().map(Student::getName).collect(Collectors.toList());
  // [byron, johnny]
  System.out.println(studentNames);
  ```

  ```java
  // map
  // 输出所有单词的长度
  List<String> stringList = Arrays.asList("Java8", "Lambdas", "In", "Action");
  List<Integer> stringLengthList = stringList.stream().map(String::length).collect(Collectors.toList());
  // [5, 7, 2, 6]
  System.out.println(stringLengthList);
  ```

- 流的扁平化

  ```java
  List<String> words = Arrays.asList("Hello", "World");
  words.stream()
  	.map(word -> word.split(""))
  	.distinct()
  	.collect(toList());
  ```

  方法的问题在于，传递给map方法的Lambda为每个单词返回了一个String[]（String列表）。因此， map 返回的流实际上是Stream<String[]> 类型的。你真正想要的是用Stream<String>来表示一个字符流.

  ![image-20200729095743724](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729095743724.png)

- flatMap

  - 使用flatMap方法的效果是，各个数组并不是分别映射成一个流，而是映射成流的内容。所有使用map(Arrays::stream)时生成的单个流都被合并起来，即扁平化为一个流。

    ![image-20200729095951008](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729095951008.png)