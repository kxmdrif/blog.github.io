---
title: Union-Find
date: 2021-01-21 21:43:13
description: Union Find
categories: Algorithms
tags: Algorithms
---

## 并查集

并查集是一种树形的数据结构，顾名思义，它用于处理一些不交集的 **合并** 及 **查询** 问题。 它支持两种操作：

- 查找（Find）：确定某个元素处于哪个子集；
- 合并（Union）：将两个子集合并成一个集合。

### 初始化

`fa[i]`是i的父节点。祖先的父节点是自己。

```java
public abstract class UF {
    private int size;
    private int[] fa;
    public UF(int size) {
        this.size = size;
        this.fa = new int[size];
        for (int i = 0; i < size; i++)
            fa[i] = i;
    }
    public abstract int find(int x);
    
    public abstract void union(int x, int y);
}
```

### 查找

寻找x的祖先

```java
public int find(int x) {
    // 如果x是祖先则返回
    if (fa[x] == x) return x;
    // 如果不是则x的爸爸问x的爷爷
    else return find(fa[x]);
}
```

### 路径压缩

这样的确可以达成目的，但是显然效率实在太低。为什么呢？因为我们使用了太多没用的信息，我的祖先是谁与我父亲是谁没什么关系，这样一层一层找太浪费时间，不如我直接当祖先的儿子，问一次就可以出结果了。甚至祖先是谁都无所谓，只要这个人可以代表我们家族就能得到想要的效果。 **把在路径上的每个节点都直接连接到根上** ，这就是路径压缩。

```java
public int find(int x) {
  if (x != fa[x])  // x不是自身的父亲，即x不是该集合的代表
    fa[x] = find(fa[x]);  // 查找x的祖先直到找到代表,于是顺手路径压缩
  return fa[x];
}
```

### 合并

并不在意祖先究竟是谁，所以只要其中一个祖先变成另一个祖先的儿子就可以了。

```java
public void unionSet(int x, int y) {
  // x 与 y 所在家族合并
  x = find(x);
  y = find(y);
  fa[x] = y;  // 把 x 的祖先变成 y 的祖先的儿子
}
```

