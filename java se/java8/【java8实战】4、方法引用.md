### 方法引用

---

#### 1、基本概念

```java
// 不使用方法引用
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));

// 使用方法引用（Apple::getWeight）
inventory.sort(comparing(Apple::getWeight));
```

#### 2、方法引用的分类

- 指向静态方法的方法引用，比如Integer::parseInt
- 指向任意类型实例方法的方法应用，比如String::length
- 指向现有对象的实例方法的方法引用

#### 3、普通Lambda和等价的方法引用

![image-20200728152459106](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200728152459106.png)

#### 4、构造函数引用

```java
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();

//等价于
Supplier<Apple> c1 = () -> new Apple();
Apple a1 = c1.get();
```

```java
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110);

// 等价于
Function<Integer, Apple> c2 = (weight) -> new Apple(weight);
Apple a2 = c2.apply(110);
```