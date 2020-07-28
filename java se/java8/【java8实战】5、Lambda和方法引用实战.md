### Lambda和方法引用实战

---

#### 1、传递代码

```java
// 定义
public class AppleComparator implements Comparator<Apple> {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }	
}

// 传递代码
inventory.sort(new AppleComparator());
```

#### 2、使用匿名类

```java
// 匿名类
inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2){
    	return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

#### 3、使用Lambda表达式

```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

#### 4、使用方法引用

```java
inventory.sort(comparing(Apple::getWeight));
```

