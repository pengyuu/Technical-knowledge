# Java 8 中的 Streams API 详解

Streams 的背景，以及 Java 8 中的使用详解

## 为什么需要 Stream

Stream 作为 Java 8 中的一大亮点，它与 java.io 包里的 InputStream 和 OutputStream 是完全不同的概念。它也不同于 StAX 对 XML 解析的 Stream，也不是 Amazon Kinesis 对大数据实时处理的 Stream。Java 8中的Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作（bulk data operation）。Stream API 借助于同样新出现的Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行方式来拆分任务和加速处理过程。同城编写并行代码很难而且容易出错，但使用Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。所以说，Java 8 中首次出现的 java.util.stream 是一个函数式语言 + 多核时代的综合影响的产物。

## 什么是聚合操作

在传统的 J2EE 应用中，Java 代码经常不得不依赖于关系型数据库的聚合操作来完成诸如：

 - 客户每月平均消费金额
 - 最昂贵的在售商品
 - 本周完成额有效订单（排除了无效的）
 - 取十个数据样本最为首页推荐

这类的操作。

但在当今这个数据大爆炸的时代，在数据来源多样化、数据海量化的今天，很多时候不得不脱离 RDBMS - 关系数据库管理系统(Relational Database Management System)，或者以底层返回的数据为基础进行更上层的数据统计。而 Java 的集合 API 中，仅有极少量的辅助性方法，更多的时候是程序员需要用 Iterator 来遍历集合，完成相关的聚合应用逻辑。这是一种远不够高效、笨拙的方法。在Java 7 中，如果要发现 type 为 grocery 的所有交易，然后返回以交易值降序排序好的交易 ID 集合，我们需要这样写：

程序清单1：Java 7的排序、取值实现

```java
List<Transaction> groceryTransactions = new Arraylist<>();
for (Transaction t : transactions) {
    if (t.getType() == Transaction.GROCERY) {
        groceryTransactions.add(t);
    }
}
Collections.sort(groceryTransactions, new Comparator() {
    public int compare(Transaction t1, Transaction t2) {
        return t2.getValue().compareTo(t1.getValue());
    }
});
List<Integer> transactionIds = new ArrayList<>();
for (Transaction t : groceryTransactions) {
    transactionsIds.add(t.getId());
}
```

而在 Java 8 中使用 Stream，代码更加简洁易读；而且使用并发模式，程序执行速度更快。

程序清单2：Java 8的排序、取值实现

```java
List<Integer> transactionsIds = transactions.parallelStream().
        filter(t -> t.getType() == Transaction.GROCERY).
        sorted(comparing(Transaction::getValue).reversed()).
        map(Transaction::getId).
        collect(toList());
```

## Stream 总览

### 什么是流 ( Stream )

Stream 不是集合元素，它不是数据结构并不保存数据，它是有关算法和计算的，它更像一个高级版本的Iterator。原始版本的 Iterator，用户只能显式地一个一个遍历元素并对其执行某些操作；高级版本的 Stream，用户只要给出需要对其包含的元素执行什么操作，比如“过滤掉长度大于10的字符串”、“获取每个字符串的首字母”等，Stream 会隐式的在内部进行遍历，做出相应的数据转换。

Stream 就如同一个迭代器（Iterator），单向，不可往复，数据只能遍历一次，遍历一次后即用尽了，就好比流水从面前流过，一去不复返。

而和迭代器又不同的是，Stream 可以并行化操作，迭代器只能命令式地、串行化操作。顾名思义，当使用串行方式去遍历时，每个 item 读完后再读下一个 item。而是用并行去遍历时，数据会被分成多个段，其中每一个都在不同的线程中处理，然后将结果一起输出。Stream 的并行操作依赖于 Java 7 中引入的 Fork/Join框架（JSR166y）来拆分任务和加速处理过程。Java 的并行 API 演变历程基本如下：

 1. 1.0 - 1.4 中的 java.lang.Thread
 1. 5.0 中的 java.util.concurrent
 1. 6.0 中的 Phasers
 1. 7.0 中的 Fork/Join 框架
 1. 8.0 中的 Lambda

Stream 的另外一大特点是：数据源本身可以是无限的。

### Stream 的构成

当我们使用一个流的时候，通常包括三个基本步骤：获取一个数据源 (source) -> 数据转换 -> 执行操作