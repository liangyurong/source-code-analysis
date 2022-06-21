## 主要过程



## 方法分析

### 1、put方法

```java
public V put(K key, V value) {
	// hash(key) : 计算hash值，并不是计算出在数组的下标索引
    return putVal(hash(key), key, value, false, true);
}
```



计算数组索引下标

```
hash &(n-1)
```



### 2、putVal



```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
    Node<K,V>[] tab; 
    Node<K,V> p; 
    int n, i;
    
    // tab为空则扩容 （第一次put，则tab==null为true）
    // tab == table ， 将table赋值给局部变量tab
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    
    // tab不为空
    // tab[i] == null , 说明该数组的i下标没有数据，因此可以直接插入
    // (n-1)&hash :  得到数组下标 （和hash%n的结果是一样的，但是(n-1)&hash的速度更快）
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // tab[i] != null ， 该数组下标的位置已经有数据
    else {
        Node<K,V> e; 
        K k;
        
        // hash一样且key一样 , 则需要替换旧值
        // p已经被赋值： p = tab[i]
        // 为什么比较hash还要比较key ： 因为可能会有key不同但是hash相同的情况，比如Aa和Bb的hash就是一样的
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k)))){
            e = p;
        }
        // hash一样，key不一样
        // 判断是树节点，将节点插入到红黑树
        else if (p instanceof TreeNode){
            // 返回一个树节点
            // e == null ， 说明产生新节点； 不为null，说明要替换
			e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        }    
        // hash一样，key不一样
        // 判断是链表节点
        // 通过循环判断，在合适的地方插入节点
        else {
            for (int binCount = 0; ; ++binCount) {
                // 节点的下一个节点如果为空，则新节点插入链表末尾
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 链表节点数 =  8 ， 则转为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                // 节点的下一个节点不为空，并且，hash和key都相同，则break跳出循环，在后续操作中会将新值替换旧值
                // e = p.next
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                
                // 节点的下一个节点不为空，且不满足 hash和key不相同，则遍历下一个节点
                p = e;
            }
        }
        // 新值替换旧值，返回旧值
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
        
    }
    
    // 每次改动hash表都会加1
    ++modCount;
    
    // 插入之后，判断是否需要扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

