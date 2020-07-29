### 使用流程(查找和匹配)

---

#### 1、匹配元素

- anyMatch, 流中是否由一个元素能匹配给定的谓词

- allMatch, 流中是否所有的元素都能匹配给定的谓词

- nonMatch, 流中是否所有的元素都不匹配给定的谓词

  ```java
  // anyMath
  List<Integer> nums = Arrays.asList(1,2,3,4,5,6,7,8,9,0);
  if(nums.stream().anyMatch(x -> 0 == x)){
      System.out.println("find 0 in nums!");
  }else{
      System.out.println("there is not 0 found in nums!");
  }
  
  // allMatch
  List<Integer> nums1 = Arrays.asList(1,1,1,1,1);
  if(nums.stream().allMatch(x -> 1 == x)){
      System.out.println("all 0 in nums!");
  }else{
      System.out.println("not all 0 in nums!");
  }
  
  // nonMatch
  if(nums.stream().noneMatch(x -> 10 == x)){
      System.out.println("there is no 10 in nums");
  }else{
      System.out.println("10 in nums");
  }
  ```

#### 2、查找元素

- findAny，返回当前流中的任意元素。

  ```java
   List<Integer> nums = Arrays.asList(1,2,3,4,5,6,7,8,9,0);
  
   // find any
   Optional<Integer> numOptional = nums.stream().filter(x -> x > 2).findAny();
   numOptional.ifPresent(System.out::print)
  ```

- findFirst， 找到第一个元素

  ```java
   List<Integer> nums = Arrays.asList(1,2,3,4,5,6,7,8,9,0);
  
   // find any
   Optional<Integer> numOptional = nums.stream().filter(x -> x > 2).findFirst();
   numOptional.ifPresent(System.out::print)
  ```

