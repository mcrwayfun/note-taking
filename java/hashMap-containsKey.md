## hashMap.containsKey(value)时间复杂度分析
### `hashMap.containsKey(value)`的时间复杂度为什么是O(1)呢？这个就要来看一下源码了
```java
 	/**
     * Returns <tt>true</tt> if this map contains a mapping for the
     * specified key.
     *
     * @param   key   The key whose presence in this map is to be tested
     * @return <tt>true</tt> if this map contains a mapping for the specified
     * key.
     */
    public boolean containsKey(Object key) {
        return getNode(hash(key), key) != null;
    }
```
调用了`getNode(hash(key), key)`方法，参数分别为**key的hash值**，**key**。再来看下这个方法的实现
```java
    /**
     * Implements Map.get and related methods
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
			// 直接命中
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
			// 未命中
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```
可以看到返回的是first(默认check first node)，算法中带n的可能影响时间复杂度的便是：
```java
first = tab[(n - 1) & hash]) != null
```
为了探究为什么是O(1)，这里就要理解
- `Node<K,V> first`是什么
- `Node<K,V>[] tab`是什么

`Node<K,V> first`：是一个单向链表结点，包含了hash，key，value和指向下个结点的指针
```java
 	/**
     * Basic hash bin node, used for most entries.  (See below for
     * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
     */
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        ... get and set method ...
    }
```
`Node<K,V>[] tab`：是一个数组，值得注意的是，数组长度为2的倍数
```java
	/**
     * The table, initialized on first use, and resized as
     * necessary. When allocated, length is always a power of two.
     * (We also tolerate length zero in some operations to allow
     * bootstrapping mechanics that are currently not needed.)
     */
    transient Node<K,V>[] table;
```
所以`tab[(n - 1) & hash]`执行了如下操作：
1. 指针first指向那一行数组的引用（那一行数组是通过table长度和key的hash值计算出来的），所以通过下标访问数组时间复杂度为O(1)
2. 如果没有直接命中（key进行hash时，产生相同的位运算值），变为红黑树的存储方式，那么遍历树的时间复杂度为O(n)

### 总结
综上，hashMap.containsKey(value)最好情况便是O(1)，最坏情况是O(n)