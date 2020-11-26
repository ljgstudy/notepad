### 数值流

---

#### 1、原始类型流特性

Java 8引入了三个原始类型特化流接口来解决这个问题：IntStream、DoubleStream和LongStream，分别将流中的元素特化为int、long和double，从而避免了暗含的装箱成本。

- 映射到数值流

  ```java
  int calories = menu.stream().mapToInt(Dish::getCalories).sum();
  ```

- 转换回对象流

  ```java
  IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
  Stream<Integer> stream = intStream.boxed();
  ```

- 默认值OptionalInt

  ```java
  OptionalInt maxCalories = menu.stream().mapToInt(Dish::getCalories).max();
  int max = maxCalories.orElse(1);
  ```

#### 2、数值范围

```java
// 0-100里取所有的偶数
IntStream evenNumbers = IntStream.rangeClosed(1, 100).filter(n -> n % 2 == 0);
System.out.println(evenNumbers.count());
```

#### 3、勾股数

```java
Stream<double[]> pythagoreanTriples2 = 
IntStream.rangeClosed(1, 100).boxed()
	.flatMap(a -> IntStream.rangeClosed(a, 100)
	.mapToObj(b -> new double[]{a, b, Math.sqrt(a*a + b*b)})
	.filter(t -> t[2] % 1 == 0));
```