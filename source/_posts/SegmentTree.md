---
title: SegmentTree
date: 2021-01-01 17:58:00
description: Segment Tree
categories: Algorithms
tags: Algorithms
---



## 线段树

### 1 介绍

线段树是一种用来维护**区间信息**的数据结构。线段树可以在`O(logN)`的时间复杂度内实现单点修改、区间修改、区间查询（区间求和，求区间最大值，求区间最小值）等操作。

### 2 存储

采用堆式存储, `2i`是`i`的左儿子, `2i + 1`是`i`的右儿子。以[a,b,c,d,e]数组为例, 存储数组结构为[0, a + b + c + d + e, a + d + e, b + c, d + e, a, b, c, d, e] (数组第一个位置不使用, 第二个位置为树的根节点)。

### 3 更新

从要更新的位置开始至下而上更新。

### 4 求和

如果左区间端点是左节点，求和时向上找，否则向右找。

同理，如果右区间端点是右节点，求和时向上找，否则向左找。



```java
class SegmentTree {

    private int n;
    
    //the size of tree is 2n
    private int[] tree;
    /*
    2 * i + 1 = i << 1 | 1 (because after * 2, the most right dight is 0, so add 1 is same to or 1) 
    */
    public SegmentTree(int[] nums) {
        n = nums.length;
        tree = new int[n << 1];
        for (int i = n; i < (n << 1); i++)
            tree[i] = nums[i - n];
        for (int i = n - 1; i > 0; i--)
            tree[i] = tree[i << 1] + tree[i << 1 | 1];
        
            
    }
    
    public void update(int i, int val) {
        i += n;
        tree[i] = val;
        while(i > 1) {
            tree[i >> 1] = tree[i] + tree[i ^ 1];
            i >>= 1;
        }
    }
    
    //sum of nums[i, j]
    public int query(int i, int j) {
        int sum = 0;
        i += n;
        j += n;
        while(i <= j) {
            if ((i & 1) == 1) sum += tree[i++];
            if ((j & 1) == 0) sum += tree[j--];
            i >>= 1;
            j >>= 1;
        }
        return sum;
        
    }
}
```

