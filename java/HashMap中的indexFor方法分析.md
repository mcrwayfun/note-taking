# HashMap中的indexFor方法分析

分析java7的HashMap源码的时候，其用indexFor来计算entry对象在table数组中的索引值
```java
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);
    }
```

为什么不是对hash表的长度取余，而使用&进行位运算呢？

```java
    /**
     * The table, resized as necessary. Length MUST Always be a power of two.
     */
    transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;

    /**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```
**DEFAULT_INITIAL_CAPACITY**是初始化数组的默认长度，初始值为16。table为存储对象的数组，由注释可以知道，其长度必定为2的次方。

那么为什么要使用**h & （length-1）**来获得下标呢？

因为length为2^n，所以length-1换算成二进制，其全部位数均为1。按位与计算的原则是两位同时为“1”，结果才为“1”，否则为“0”。所以对于计算表达式**h & （length-1）**来说，等同于返回**h的低（length-1）位**，与h%length相同，但是快很多。

举个例子，假设h为1000，length为512，
```
	hash h 11 1110 1000 1000 in decimal
  length l 10 0000 0000 512 in decimal
  length-1 01 1111 1111 511 in decimal-it will always be all Ones
  h &(l-1) 01 1110 1000 488，488 in decimal which is a result of 1000 mod 512 
```
由上述例子可以看出，**h&(length-1)**和**h%length**得出的结果是一样的（返回**h的低（length-1）位**），但是位运算的速度会更快些


