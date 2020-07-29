### 使用流（规约）

---

#### 1、元素求和/乘积

```java
List<Integer> nums = Arrays.asList(1,2,3);

// 加和
Integer sumResult = nums.stream().reduce(0, (x, y) -> x + y);
System.out.println("sumResult: " + sumResult);

// 乘积
Integer multiResult = nums.stream().reduce(1, (x, y) -> x * y);
System.out.println("multi result: " + multi
```

![image-20200729152952858](https://ali-oss-file-bucket.oss-cn-shanghai.aliyuncs.com/image-20200729152952858.png)

#### 2、最大值和最小值

```

```



