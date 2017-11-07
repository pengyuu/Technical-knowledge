## JDK8 类和接口的多继承

以前经常谈论的 Java 对比 C++ 的一个优势是 Java 中没有多继承的问题. 因为 Java 中子类只能继承 (`extends`) 单个父类, 尽管可以实现 (`implement`) 多个接口, 但是接口之中只有抽象方法, 方法体是空的, 没有具体的方法实现, 不会有方法冲突的问题.

在 Java8 发布后, 接口中也可以定义方法了(default method). 之所以打破之前的设计在接口中增加具体的方法, 是为了既有的成千上万的 Java 类库的类增加薪的功能, 且不必对这些类重新进行设计. 比如, 只需在 Collection 接口中增加 `default Stream<E> stream()`, 相应的 Set 和 List 接口以及它们的子类都包含此方法, 不必为每个子类都重新copy这个方法.

这是一个折中的设计, 带来的问题就是为 Java 引入了多继承的问题. 接口可以继承接口, 类可以继承类和实现接口, 一旦继承的类和实现的接口中有相同签名的方法, 会出现什么样的状况? 本文将探讨各种情况的多继承, 以便能清楚的理解 Java 多继承的规则.

### 接口继承多个父接口

假定有三个接口 Interface A, Interface B, Interface C 继承关系:
```java
interface C extends A, B
```

A, B 拥有相同方法的名, 返回值, 参数的默认方法 default String(String name), 如果接口 C 没有 override 这个方法, 则编译出错.
```java
interface A {
  default String say(String name){
    return "A: "+ name
  }
}

interface B {
  default String say(String name){
    return "B: "+ name
  }
}

interface C extends A, B {

}
```

错误信息:
```bash
error: interface C inherits unrelated defaults for say(String) from types A and B
        static interface C extends A,B{
               ^
1 error
```

我们可以在子接口 C 中覆盖( `override` )这个方法, 这样编译就不会出错了:
```java
interface C extends A, B{
  default String say(String name){
    return "C: " + name
  }
}
```
##### 方法名相同; 返回值, 方法参数不同

注意在方法名相同, 但返回值, 方法参数不同, 情况有些区别:

```java
public interface A {
    default String say(String name) {
        System.out.println("A: " + name);
        return "A: " + name;
    }
}

public interface B {
    default String say(int name) {
        System.out.println("B: " + name);
        return "B: " + name;
    }
}

public interface C extends A, B {
    @Override
    default String say(String name) {//参数为 String name
        System.out.println("C: " + name);
        return "C: " + name;
    }
}

public class D implements C {
}

public static void main(String[] args) {
  D d = new D();
  d.say(1);//System.out.println输出 B: 1
  d.say("1");//System.out.println输出 C: 1
}

//C还有另一种情况
public interface C extends A, B {
    @Override
    default String say(int name) {//参数为 int name
        System.out.println("C: " + name);
        return "C: " + name;
    }
}

public static void main(String[] args) {
  D d = new D();
  d.say(1);//System.out.println输出 B: 1
  d.say("1");//System.out.println输出 A: 1
}
```
class D 有两种 `say` 方法:
![](/images/multi_interface_01.png)

##### 方法名, 参数相同; 返回值不同
但在方法名, 参数相同, 返回值不同时 C 无法同时继承 A, B:
```java
public interface A {
    default String say(String name) {
        System.out.println("A: " + name);
        return "A: " + name;
    }
}

public interface B {
    default int say(String name) {
        System.out.println("B: " + name);
        return 1;
    }
}
```
![](/images/multi_interface_02.png)

![](/images/multi_interface_03.png)

##### 方法名相同, 参数不相同, 返回值不同

方法名相同, 参数不相同, 返回值不同, C可以同时 `Override` A, B的方法
```java
public interface A {
    default String say(String name) {
        System.out.println("A: " + name);
        return "A: " + name;
    }
}

public interface B {
    default int say(int name) {
        System.out.println("B: " + name);
        return name;
    }
}

public interface C extends A, B {
    @Override
    default String say(String name) {
        System.out.println("C: " + name);
        return name;
    }

    @Override
    default int say(int name) {
        System.out.println("C: " + name);
        return name;
    }
}

public class D implements C {
}

public static void main(String[] args) {
  D d = new D();
  d.say(1);//System.out.println输出 C: 1
  d.say("1");//System.out.println输出 C: 1
}
```

### 接口的多层继承

假定有三个接口 Interface A, Interface B, Interface C 继承关系:
```java
public interface A {
    default void say(String name) {
        System.out.println("A: " + name);
    }
}

public interface B extends A{
    @Override
    default void say(String name) {
        System.out.println("B: "+name);
    }
}

public interface C extends B{
    @Override
    default void say(String name) {
        System.out.println("C: " + name);
    }
}

public class D implements C {
}

public static void main(String[] args) {
  D d = new D();
  d.say("1");//System.out.println(); 输出 C: 1
}
```

这个没有意外

### 类继承
```java
public interface A {
    default void say(String name) {
        name = "A: " + name;
        System.out.println( name);
    }
}

public class B{
  public void say(String name) {
       name = "B: "+name;
       System.out.println(name);
   }
}

public class C extends B implements A {

}

public static void main(String[] args) {
  C c = new C();
  c.say("1");//System.out.println(); 输出 B: 1
}
```

可以看出， 子类优先继承父类的方法

### 结论

其他更复杂的继承关系可以简化为以上的继承关系.

根据以上的例子, 可以得出以下的结论:

 - 类优先于接口. 如果一个子类继承的父类和实现的接口有相同的方法(方法名, 参数, 返回值都相同), 那么子类继承父类的方法
