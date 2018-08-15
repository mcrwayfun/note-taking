# HashMap
hashMap是经典的key-value存储结构， HashMap 底层是基于 数组 + 链表 组成的，不过在 jdk1.7 和 1.8 中具体实现稍有不同。

## java7的hashMap
```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

static final int MAXIMUM_CAPACITY = 1 << 30;

static final float DEFAULT_LOAD_FACTOR = 0.75f;

static final Entry<?,?>[] EMPTY_TABLE = {};

transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;

transient int size;

int threshold;

final float loadFactor;
```

- DEFAULT_INITIAL_CAPACITY：初始桶的大小，即数组的默认长度
- MAXIMUM_CAPACITY：桶的最大长度
- DEFAULT_LOAD_FACTOR：负载因子（0.75）
- table：存放数据的数组，数组长度一定为2的倍数
- size：map存放的数量大小
- threshold：桶的大小
- loadFactor：负载因子，可显示指定