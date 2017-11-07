## Java简介

Java分为三分体系:
 1. JavaSE(Java2 Platform Standard Edition, Java标准版)
 2. JavaEE(Java2 Platform Enterprise Edition, Java企业版)
 3. JavaME(Java2 Platform Micro Edition Java微型版)

### 主要特性

 - 简单:

   Java语言的语法与C和C++很接近, 使得大多数程序员很容易学习和使用. 另一方面Java抛弃了C++中很少使用的, 很难理解的, 令人迷惑的的那些特性, 如操作符重载, 多继承, 自动的强制类型转换. 特别的, Java语言不使用指针, 而是引用. 并提供自动的垃圾回收, 程序员不需为内存管理担忧


 - 面向对象

Java语言提供类, 接口和继承等面向对象的特性; 为了简单, 只支持类之间的单继承, 但支持接口之间的多继承`public interface C extends A,B {}`, 并支持类与接口之间的实现机制(关键字为`implement`). Java语言全面支持动态绑定, 而C++语言只对虚函数使用动态绑定. 总之Java语言是一种面向对象编程语言.
