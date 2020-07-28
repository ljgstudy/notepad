### 复合Lambda表达式

---

#### 1、比较器复合

- 逆序

  ```java
  // 按照重量倒序
  inventory.sort(comparing(Apple::getWeight).reversed());
  ```

- 比较器链

  ```java
  // 先按照重要倒序，然后按照国家升序
  inventory.sort(comparing(Apple::getWeight)
  .reversed()
  .thenComparing(Apple::getCountry));
  ```

#### 2、谓词复合

- negate

  ```java
  // 非红苹果
  Predicate<Apple> notRedApple = redApple.negate();
  ```

- and

  ```java
  // 大于150g的红苹果
  Predicate<Apple> redAndHeavyApple = redApple.and(a -> a.getWeight() > 150);
  ```

- or

  ```java
  // 150g以上的红苹果或者绿苹果
  Predicate<Apple> redAndHeavyAppleOrGreen = redApple.and(a -> a.getWeight() > 150).or(a -> "green".equals(a.getColor()));
  ```

#### 3、函数复合

- andThen

  ```java
  // 输出结果为4
  Function<Integer, Integer> f = x -> x + 1;
  Function<Integer, Integer> g = x -> x * 2;
  Function<Integer, Integer> h = f.andThen(g);
  int result = h.apply(1);
  ```

- compose

  ```java
  // 输出结果为3
  Function<Integer, Integer> f = x -> x + 1;
  Function<Integer, Integer> g = x -> x * 2;
  Function<Integer, Integer> h = f.compose(g);
  int result = h.apply(1);
  ```

- 两者区别

  ![image-20200728154329304](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728154329304.png)

