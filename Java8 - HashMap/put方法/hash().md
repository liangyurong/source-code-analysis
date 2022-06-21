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
```

