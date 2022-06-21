## hash()

计算key计算hash值，得到在数组的下标索引

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```



计算步骤

```
key的hashcode()方法  异或  h右移16位
```

为什么采用这种方法？

```
让key的hashcode值与hashcode值的高16位进行异或计算，是为了让数组下标更加散列
```

异或运算

```
0 0 1
0 1 1
1 0 1
1 1 0
```

为什么会让数组下标更加散列

```
数组下标的计算：index =  h & (n-1)

h = hash(key)

怎么让index的值更加散列？

	干扰函数(h = key.hashCode()) ^ (h >>> 16)加大了低位的随机性
```

