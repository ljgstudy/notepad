### 函数接口及环绕执行模式

---

#### 1、基本概念

- 函数式接口就是只定义一个抽象方法的接口，JDK中的例子如下：

  ```java
  @FunctionalInterface
  public interface Comparator<T> {
      int compare(T o1, T o2);
      
      // 从object类中继承，不违背函数式接口只有一个抽象方法的原则
      boolean equals(Object obj);
  }
  ```

  根据Java语言规范的定义，一个使用了该注释的接口类型声明将被视为一个函数式接口。从概念上讲，一个函数式接口有且只有一个抽象方法。

  由于默认方法已经有了实现，所以它们不是抽象方法。如果一个接口中声明的抽象方法是重写了超类Object类中任意一个public方法，那么这些抽象方法并不会算入接口的抽象方 法数量中。因为任何接口的实现都会从其父类Object或其它地方获得这些方法的实现。

  ```java
  @FunctionalInterface
  public interface Runnable {
      public abstract void run();
  }
  ```

  ```java
  public interface ActionListener extends EventListener {
  
      /**
       * Invoked when an action occurs.
       */
      public void actionPerformed(ActionEvent e);
  }
  ```

  ```java
  @FunctionalInterface
  public interface Callable<V> {
      /**
       * Computes a result, or throws an exception if unable to do so.
       *
       * @return computed result
       * @throws Exception if unable to compute a result
       */
      V call() throws Exception;
  }
  ```

  - Lambda表达式允许你直接以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例，具体说来，是函数式接口一个具体实现的实例

#### 2、函数描述符

- 函数式接口的抽象方法的签名基本上就是Lambda表达式的签名。我们将这种抽象方法叫做函数描述符
- () -> expression, 表示参数列表为空
- Lambda表达式的签名要和函数式接口的抽象方法一致
- @FunctionalInterface定义了一个接口，而它却不是函数式接口的话，编译器将返回一个提示原因的错误

#### 3、使用函数接口

- 定义函数接口

  ```java
  @FunctionalInterface
  public interface Predicate<T> {
      /**
       * test
       * @param t
       * @return 
       * @author byron li
       * @date 2020/7/28 11:02
       */
      boolean test(T t);
  }
  ```

- 使用函数接口

  ```java
  public class TestApp {
      public static void main(String[] args) {
  
          List<Integer> nums = Arrays.asList(1,2,3, 4,5);
          List<Integer> result = filter(nums, x -> x > 1);
  
          System.out.println(result);
      }
  
      private static <T> List<T> filter(List<T> list, Predicate<T> p){
          List<T> results = new ArrayList<>();
  
          for(T t : list){
              if(p.test(t)){
                  results.add(t);
              }
          }
          
          return results;
      }
  }
  ```

#### 4、环绕执行模式

- 资源处理（例如处理文件或者数据库）时一个常见的模式就是打开一个资源，做一些处理，然后关闭资源。这个设置和清理阶段总是很类似，并且会围绕着执行处理的那些重要代码，这就是所谓的环绕执行（execute around）模式。

  ![image-20200728111214770](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728111214770.png)

- 应用环绕执行模式所采用的四个步骤

  ![image-20200728111847559](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728111847559.png)