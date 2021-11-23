## 基本数据类型



## 缓存池
缓存池的大小默认为 -128~127,Integer.ValueOf()会使用缓存池中得对象，多次调用会取得同一对象引用。

> 在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

```java
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

## 字符串

> 在 Java 8 中，String 内部使用 char 数组存储数据。
>
> 在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。

### 特性

不可变

> 字符串不可变的好处：
>
> 1. 可以缓存hash值
> 2. 字符串常量池的需要
> 3. 安全性
> 4. 线程安全

## 初始化顺序

> 存在继承：
>
> - 父类（静态变量、静态语句块）
> - 子类（静态变量、静态语句块）
> - 父类（实例变量、普通语句块）
> - 父类（构造函数）
> - 子类（实例变量、普通语句块）
> - 子类（构造函数）

