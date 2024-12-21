# 哈希表


# hashtable

## ***hashtable***

1. ***什么叫hashtable***
   - *哈希表也叫做散列表，是根据关键码值而直接进行访问的数据结构*
   - *他通过把关键码值映射到表中的一个位置来访问记录，以加快查找的速度*
   - *这个映射函数叫做散列函数，存放的记录叫做哈希表*
2. ***hashtable的时间复杂度***
   - ***search：**O(1)*
   - ***Insertion：**O(1)*
   - ***Deletion：**O(1)*
   - ***但是我们如果了解什么是哈希冲突 以及拉链法解决哈希冲突，那么在哈希表很小并且冲突很多的情况下，时间复杂度会退化成链表O(n)***
3. ***hashtable的原理和golang的map实现 图示***
   - ![map的底层实现](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesmap%E7%9A%84%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0.jpg)

## ***hashtable例题***

```go
// https://leetcode.cn/problems/reverse-linked-list/
// https://leetcode.com/problems/swap-nodes-in-pairs

// https://leetcode.com/problems/linked-list-cycle  
// https://leetcode.com/problems/linked-list-cycle-ii
```

