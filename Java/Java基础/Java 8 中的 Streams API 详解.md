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

当我们使用一个流的时候，通常包括三个基本步骤：获取一个数据源 (source) -> 数据转换 -> 执行操作获取想要的结果，每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象(可以有多次转换)，这就允许对其操作可以像链条一样排列，编程一个管道，如下图所示。

图1.流管道(Stream Pipeline)的构成

![](./images/stream001.png)

有多种方式生成 Stream Source:
 - 从 Collection 和数组
    - Collection.stream()
    - Collection.parallelStream()
    - Arrays.stream(T array) / Stream.of()
 - 从 BufferedReader
   - java.io.BufferedReader.lines()
 - 静态工厂
   - java.util.stream.IntStream.range()
   - java.nio.Files.walk()
 - 自己构建
   - java.util.Spliterator
 - 其他
   - Random.ints()
   - BitSet.stream()
   - Pattern.splitAsStream(java.lang.CharSequence)
   - JarFile.stream()

Stream 的操作类型分为两种：
 - Intermediate:一个 Stream 可以后面跟随零个或多个 intermediate 操作。其目的主要是打开 Stream，做出某种程度的数据映射/过滤，然后返回一个新的 Stream，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始 Stream 的遍历。
 - Terminal:一个 Stream 只能有一个 terminal 操作,当这个操作执行后，Stream就被使用“光”了，无法再被操作。所以这必定是 Stream 的最后一个操作。terminal 操作的执行，才会是真正开始 Stream 的遍历，并且会生成一个结果，或者一个 side effect。

再对于一个 Stream 进行多次转换操作（Intermediate 操作），每次都对 Stream的每个元素进行转换，而且是执行多次，这样的时间复杂度就是 N（转换次数）个 for 循环里把所有操作掉的总和吗？其实不是这样的，转换操作都是 lazy 的，多个转换操作只会在 Terminal 操作的时候融合起来，一次循环完成。我们可以这样简单的理解，Stream 里有个操作函数的集合，每次转换操作就是把转换函数放入这个集合中，在 Terminal 操作的时候循环 Stream 对应的集合，然后对每个元素执行所有的函数。

还有一种操作被称为 short-circuting。用以指：
 - 对于一个 itermediate 操作，如果它接受的是一个无限大（infinite/unbounded）的 Stream，但返回一个有限的新 Stream。
 - 对于一个 terminal 操作，如果它接受的是一个无限大的 Stream，但能在有限的时间计算出结果。

当操作一个无限大的 Stream，而又希望咋有限的时间内完成操作，则在管道内拥有一个 short-circuting 操作是必要非充分条件。

程序清单 3.一个 Stream 操作的示例

```java
int sum = widgets.stream()
    .filter(w -> w.getColor() == RED)
    .mapToInt(w -> w.getWeight())
    .sum();
```

stream()获取当前小物件的 source, fliter 和 mapToInt 为 intermediate 操作，进行数据筛选和转换，最后一个 sum() 为 terminal 操作，对符合条件的全部小物件做重量求和。

### Stream 的使用详解

简单说，对 Stream 的使用就是实现一个 fliter-map-reduce 过程，产生一个最终结果，或者导致一个副作用(side effect)。

#### Stream 的构造与转换

下面提供最常见的几种构造 Stream 的样例。

程序清单 4. 构造 Stream 的几种常见方法

```java
// 1. Individual values
Stream stream = Stream.of("a", "b", "c");
// 2. Arrays
String [] strArray = new String[] {"a", "b", "c"};
stream = Stream.of(strArray);
stream = Arrays.stream(strArray);
// 3. Collections
List<String> list = Arrays.asList(strArray);
stream = list.stream();
```

需要注意的是，对于基本数值型，目前有三种对应的包装类型 Stream：IntStream、LongStream、DoubleStream。当然我们也可以用 Stream<Interger>、Stream<Long> 、Stream<Double>，但是 boxing 和 unboxing 会很耗时，所以特别为这三种基本数值类型提供了对应的 Stream。

Java 8中还没有提供其他数值型 Stream，因为这将导致扩增的内容较多。而常规的数值型聚合运算可以通过上面三种 Stream 进行。

程序清单 5.数值 Stream 的构造

```java
IntStream.of(new int[]{1, 2, 3}).forEach(System.out::println);
IntStream.range(1, 3).forEach(System.out::println);
IntStream.rangeClosed(1, 3).forEach(System.out::println);
```

程序清单 6.Stream 转换为其他数据结构

```java
// 1. Array
String[] strArray1 = stream.toArray(String[]::new);
// 2. Collection
List<String> list1 = stream.collect(Collectors.toList());
List<String> list2 = stream.collect(Collectors.toCollection(ArrayList::new));
Set set1 = stream.collect(Collectors.toSet());
Stack stack1 = stream.collect(Collectors.toCollection(Stack::new));
// 3. String
String str = stream.collect(Collectors.joining()).toString();
```

一个 Stream 只可以使用一次，上面的代码为了简洁而重复使用的数次。

#### Stream的操作

接下来，当把一个数据结构包装成 Stream 后，就要开始对立面的元素进行各类操作了。常见的操作可以归类如下。

 - Intermediate : map (mapToInt, flatMap 等) 、fliter 、distinct、sorted、peek、limit、skip、parallel、sequential、unordered
 - Terminal : forEach、forEachOrdered、toArray、reduce、collect、min、max、count、anyMatch、allMatch、noneMatch、findFirst、findAny、iterator
 - Short-circuiting : anyMatch、noneMatch、findFirst、findAny、limit

我们下面看一下 Stream 的比较典型用法。

##### map/flatMap

我们先来看 map。如果你熟悉 scala 这类函数式语言，对这个方法应该很了解，它的作用就是把 inputStream 的每一个元素，映射成 outputStream 的另外一个元素。

程序清单 7.转换大写

```java
List<String> output worldList.stream().
    map(String::toUppercase).
    collect(Collectors.toList());
```

这段代码把所有的单词转换为大写。

程序清单 8.平方数

```java
List<Integer> nums = Arrays.asList(1,2,3,4);
List<Interger> squareNums = num.stream().
    map(n -> n*n).
    collect(Collectors.toList());
```

这段代码生成一个整数 list 的平方数 {1, 4, 9, 16}。

从上面例子可以看出，map 生成的是 1:1映射，每个输入元素，都按照规则转换成另外一个元素。还有一些场景，是一对多映射关系的，这时需要 flatMap。

程序清单 9.一对多

```java
Stream<List<Integer>> inputStream = Stream.of(
    Arrays.asList(1),
    Arrays.asList(2, 3),
    Arrays.asList(4, 5, 6)
    );
Stream<Integer> outputStream = inputStream.
    flatMap((childList) -> childList.stream());
```

flatMap 把 inputStream 中的层级结构扁平化，就是将底层元素抽出来放到一起，最终 output的新Stream 立面已经没有 List l，都是之金额的数字。

##### fliter

fliter 对原始 Stream 进行某项测试，通过测试的元素被留下来生成一个新 Stream。

程序清单 10.留下偶数
```java
Interger[] sixNums = {1, ,2 ,3 , 4, 5, 6};
Interger[] evens = Stream.of(sixNums)
    .fliter(n -> n%2 == 0)
    .toArray(Integer[]::new);
```

经过条件"被2整除"的 fliter，剩下的数字为{2, 4, 6}。

程序清单 11.筛选单词

```java
List<String> output = reader.lines().
    flatMap(line -> Stream.of(line.split(REGEXP))).
    filter(word -> word.length() > 0).
    collect(Collectors.toList());
```

这段代码首先把每行的单词用 flatMap 整理到新的 Stream，然后保留长度不为 0 的，就是整篇文章中的全部单词了。

##### forEach

forEach 方法接收一个 Lambda 表达式，然后在 Stream 的每一个元素上执行该表达式。

清单 12. 打印姓名（forEach 和 pre-java8 的对比）

```java
// Java 8
roster.stream()
    .filter(p -> p.getGender() == Person.Sex.MALE)
    .forEach(p -> System.out.println(p.getName()));
// Pre-Java 8
for (Person p : roster) {
    if (p.getGender() == Person.Sex.MALE) {
        System.out.println(p.getName());
    }
}
```

对一个人员集合遍历，找出男性并打印姓名。可以看出来，forEach 是为 Lambda 而设计的，保持了最紧凑的风格。而且 Lambda 表达式本身是可以重用的，非常方便。当需要为多核系统优化时，可以 parallelStream().forEach()，只是此时原有元素的次序没法保证，并行的情况下将改变串行时操作的行为，此时 forEach 本身的实现不需要调整，而 Java8 以前的 for 循环 code 可能需要加入额外的多线程逻辑。

但一般认为，forEach 和常规 for 循环的差异不涉及到性能，它们仅仅是函数式风格与传统 Java 风格的差别。

另外一点需要注意，forEach 是 terminal 操作，因此它执行后，Stream 的元素就被“消费”掉了，你无法对一个 Stream 进行两次 terminal 运算。下面的代码是错误的：

```java
stream.forEach(element -> doOneThing(element));
stream.forEach(element -> doAnotherThing(element));
```

相反，具有相似功能的 intermediate 操作 peek 可以达到上述目的。如下是出现该 api javadoc 上的一个示例。

程序清单 13.peek 对每个元素执行操作并返回一个新的 Stream

```java
Stream.of("one", "two", "three", "four")
    .filter(e -> e.length() > 3)
    .peek(e -> System.out.println("Filter value:" + e))
    .map(String::toUpperCase)
    .peek(e -> System.out.println("Mapped value: " + e))
    .collect(Collectors.toList());
```

forEach 不能修改自己包含的本地变量值，也不能用 break/return 之类的关键字提前结束循环。

##### findFirst

这是一个 terminal 兼 short-circuiting 操作，它总是返回 Stream 的第一个元素，或者空。

这里比较重点的是它的返回值类型：Optional。这也是一个模仿 Scala 语言中的概念，作为一个容器，它可能含有某值，或者不包含。使用它的目的是尽可能避免 NullPointerException。

程序清单 14.Optional 的两个用例

```java
String strA = " abcd ", strB = null;
print(strA);
print("");
print(strB);
getLength(strA);
getLength("");
getLength(strB);

public static void print(String text) {
    // Java 8
    Optional.ofNullable(text).ifPresent(System.out::println);
    // Pre-Java 8
    if (text != null) {
        System.out.println(text);
    }
}

public static int getLength(String text) {
    // Java 8
    return Optional.ofNullable(text).map(String::length).orElse(-1);
    // Pre-Java 8
    // return if (text != null) ? text.length() : -1;
};
```

在更复杂的 if( xx != null)的情况中,使用 Optional 代码的可读性更好，而且它提供的是编译时检查，能极大的降低NullPointerException 这种 Runtime Exception 对程序的影响，或者迫使程序员更早的在编码阶段处理空值问题，而不是留到运行时再发现和调试。

Stream 中的 findAny、max/min、reduce 等方法返回 Optional 值。还有例如 IntStream.average() 返回 OptionalDouble 等等。

###### reduce