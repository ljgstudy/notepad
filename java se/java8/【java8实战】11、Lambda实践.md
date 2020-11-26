### Lambda实践

---

```java
// 基础数据
Trader raoul = new Trader("Raoul", "Cambridge");
Trader mario = new Trader("Mario","Milan");
Trader alan = new Trader("Alan","Cambridge");
Trader brian = new Trader("Brian","Cambridge");
List<Transaction> transactions = Arrays.asList(
        new Transaction(brian, 2011, 300),
        new Transaction(raoul, 2012, 1000),
        new Transaction(raoul, 2011, 400),
        new Transaction(mario, 2012, 710),
        new Transaction(mario, 2012, 700),
        new Transaction(alan, 2012, 950)
);

// 1、 找出2011年发生的所有交易，并按交易额排序（从低到高）
List<Transaction> transactionListOf2011
        = transactions.stream()
        .filter(x -> x.getYear() == 2011)
        .sorted(Comparator.comparing(Transaction::getValue))
        .collect(Collectors.toList());
System.out.println("2011年所有交易交易额正序排列: " + transactionListOf2011);

// 2、交易员都在哪些不同的城市工作过
List<String> workCities
        = transactions.stream()
        .map(x -> x.getTrader().getCity())
        .distinct()
        .collect(Collectors.toList());
System.out.println("交易员工作城市：" + workCities);

// 3、查找所有来自于剑桥的交易员，并按姓名排序
List<Trader> cambridgeTraderList
        = transactions.stream()
        .filter(x -> "Cambridge".equals(x.getTrader().getCity()))
        .map(x -> x.getTrader())
        .sorted(Comparator.comparing(Trader::getName))
        .collect(Collectors.toList());
System.out.println("剑桥交易员按照姓名排序：" + cambridgeTraderList);

// 4、返回所有交易员的姓名字符串，按字母顺序排序
List<String> sortedTraderName
        = transactions.stream()
        .map(x -> x.getTrader().getName())
        .distinct()
        .sorted()
        .collect(Collectors.toList());
System.out.println("字母顺序的所有交易员姓名：" + sortedTraderName);

// 5、有没有交易员是在米兰工作的
boolean isAnyTraderInMilan = transactions.stream().anyMatch(x -> "Milan".equals(x.getTrader().getCity()));
System.out.println("有没有在米兰工作的交易员：" + isAnyTraderInMilan);

// 6、打印生活在剑桥的交易员的所有交易额
List<Integer> allCambridgeTraderTrasactionValue
        = transactions.stream()
        .filter(x -> "Cambridge".equals(x.getTrader().getCity()))
        .map(x -> x.getValue())
        .collect(Collectors.toList());
System.out.println(allCambridgeTraderTrasactionValue);

// 7、所有交易中，最高的交易额是多少
int maxTransactionValue
        = transactions.stream()
        .map(x -> x.getValue())
        .reduce(0, Integer::max);
System.out.println(maxTransactionValue);

// 8、找到交易额最小的交易
Transaction minTransaction
        = transactions.stream()
        .reduce((t1, t2) -> t1.getValue() > t2.getValue() ? t2 : t1)
        .orElse(null);
System.out.println(minTransaction);
```

