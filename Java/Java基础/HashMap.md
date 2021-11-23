## Q1:HashMap底层数据结构
底层是hash数组+单项链表，JDK8后采用数组+链表+红黑树；默认数组长度DEFAULT_INITIAL_CAPACITY = 1 << 4; //16,当超过12长度时，采用两倍扩容，DEFAULT_LOAD_FACTOR = 0.75f用于判断HashMap扩容系数;

![hashmap结构图](https://cdn.jsdelivr.net/gh/whgx/HexoStaticFile/img/20210716151011.png)

## Q2:HashMap中hash(Object key)的原理
![image-20210716151745097](https://cdn.jsdelivr.net/gh/whgx/HexoStaticFile/img/20210716151748.png)

首先这个方法的返回值还是一个哈希值。为什么不直接返回key.hashCode()呢？还要与 (h >>> 16)异或。

![image-20210716154820509](https://cdn.jsdelivr.net/gh/whgx/HexoStaticFile/img/20210716154823.png)

![image-20210716155431568](https://cdn.jsdelivr.net/gh/whgx/HexoStaticFile/img/20210716155433.png)

![image-20210716155336444](https://cdn.jsdelivr.net/gh/whgx/HexoStaticFile/img/20210716155337.png)	

- Hash ----(h = key.hashCode()) ^ (h >>> 16)  低16bit ^ 高16bit 来确保值分散性

由于和（n-1）运算，length 绝大多数情况小于2的16次方。所以始终是hashcode 的低16位（甚至更低）参与运算。要是高16位也参与运算，会让得到的下标更加散列。
​	所以这样高16位是用不到的，如何让高16也参与运算呢。所以才有hash(Object key)方法。让他的hashCode()和自己的高16位^运算。所以(h >>> 16)得到他的高16位与hashCode()进行^运算。











> [Java 8系列之重新认识HashMap - 美团技术团队 (meituan.com)](https://tech.meituan.com/2016/06/24/java-hashmap.html)
>
> [Java HashMap 1.8 底层原理解析_以后的今天的博客-CSDN博客](https://blog.csdn.net/change987654321/article/details/80651899)
