### 类型检查、类型推断及限制

---

#### 1、类型检查

```java
List<Apple> heavierThan150g = 
    filter(inventory, (Apple a) _ > a.getWeight() > 150);
```

- 类型检查过程

  1.  找出filter方法的声明
  2. 要求它是Predicate<Apple>(目标类型)对象的第二个正式参数
  3. Predicate<Apple>是一个函数式接口，定义了一个叫做test的抽象方法
  4. test方法描述了一个函数描述符，它可以接受一个Apple，并返回一个boolean
  5. filter的任何实际参数都必须匹配这个要求

- 类型检查的过程如下

  ![image-20200728134608901](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728134608901.png)

#### 2、同样的Lambda，不同的函数式接口

- 同一个Lambda表达式就可以与不同的函数式接口联系起来，只要它们的抽象方法签名能够兼容。比如，Callable和PrivilegedAction，这个两个接口都代表着什么也不接受且返回一个泛型T的函数。因此以下两个赋值都是有效的
  ```java
  Callable<Integer> c = () -> 42
  PrivilegedAction<Integer> p = () -> 42
  ```

- 如果一个Lambda的主体是一个语句表达式，它就和一个返回void的函数描述符兼容（当然需要参数列表也兼容）。例如

  ```java
  // Predicate返回了一个boolean
  Predicate<String> p = s -> list.add(s);
  // Consumer返回了一个void
  Consumer<String> b = s -> list.add(s);
  ```

- 以下代码为啥会报错

  ```java
  Object o = () -> {System.out.println("Tricky example"); };
  ```

  答案：Lambda表达式的上下文是Object（目标类型）。但Object不是一个函数式接口。
  为了解决这个问题，你可以把目标类型改成Runnable，它的函数描述符是() -> void：

  ```java
  Runnable r = () -> {System.out.println("Tricky example"); };
  ```

#### 3、类型推断

- 什么样的代码需要类型推断

  ```java
  List<Apple> greenApples = filter(inventory, a -> "green".equals(a.getColor()));
  ```

  a并没有显式指定类型，Java编译器会推断Lambda的参数类型

- 类型推断对比

  ```java
  // 不需要类型推断
  Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
  // 需要类型推断
  Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
  ```

#### 4、Lambda中使用局部变量

- Lambda表达式中允许使用自由变量（不是参数，而是在外层作用域定义的变量）例如

  ```java
  int portNumber = 1337;
  Runnable r = () -> System.out.println(portNumber);
  ```

- 局部变量必须显式声明为final或者事实上是final的

- 局部变量的局限性

  - 实例变量和局部变量背后的实现有一个关键不同。实例变量都存储在堆中，而局部变量则保存在栈上。如果Lambda可以直接访问局部变量，而Lambda是在一个线程中使用的，则使用Lambda的线程，可能会在分配改变量的线程将这个变量回收之后，去访问改变量。因此，Java在访问自由局部变量时，实际上是在访问它的副本，而不是访问原始变量。如果局部变量仅仅赋值一次那就没有什么区别了- 因此就有了这个限制
  - 这一限制不鼓励你使用改变外部变量的典型命令式编程模式

#### 5、闭包

- 闭包是一个函数的实例，并可以无限的访问那个函数的非本地变量

- 局部变量不可以，因为它在栈上，有可能引入线程不安全的问题

- 实例变量可以，因为他在堆上，堆上的变量是线程共享的

  ```java
  public class App {
      public static void main(String[] args) {
          int i = 1;
          Student s = new Student();
          Runnable r = () -> {
              System.out.println(i);
              System.out.println(s);
          };
  
          // 编译错误
          // i++;
  
          // 编译通过
          s.setName("byron");
      }
  }
  ```