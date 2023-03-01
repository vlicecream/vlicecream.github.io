# 二分法


# ***二分法***

## 什么是二分法

***二分法**（dichotomy）指的是将一个整体事物分割成两部分。也即是说，这两部分必须是[互补事件](https://zh.wikipedia.org/wiki/互補事件)，即所有事物必须属于双方中的一方，且[互斥](https://zh.wikipedia.org/wiki/互斥)，即没有事物可以同时属于双方*

## ***利用二分法的场景***

*在排序的数组中，并且是查询操作，即可尝试二分法*

## 二分法的代码模板

```cpp
int left = 0, right = size(nums)-1;
while (right >= left) {
  int mid = left + ((right - left) >> 1); // 防止计算时溢出 & 使用了位运算
  if (nums[mid] == target) return mid;
  if (nums[mid] < target) left = mid + 1;
  if (nums[mid] > target) right = mid - 1;
}
```

```go
left, right := 0, len(array)-1
for left <= right {
  mid := left + ((right - left) >> 1) // 防止计算时溢出 & 使用了位运算
  if array[mid] == target {
    break // break or return result
  } elif array[mid] < target {
     left = mid + 1
  } else {
    right = mid - 1
  }
}
```

```python
left, right = 0, len(array)-1
while left <= right:
  mid = left + (right - left) / 2
  if arrat[mid] == target:
    break  # break or return result
  elif array[mid] < target:
    left = mid + 1
  else:
    right = mid - 1
```

# 例题

## 例题1 - 搜索插入位置

1. ***题目url***

   - *https://leetcode-cn.com/problems/search-insert-position/*

2. ***解题思路***

   - *有序数据 还是select操作 果断二分法*

3. ***题解代码***

   - ```go
     func searchInsert(nums []int, target int) int {
       left, right, ans := 0, len(nums) -1, len(nums)
       for left <= right {
         mid := (right + left)/2
         if nums[mid] >= target {
           ans = mid
           right = mid - 1
         } else {
           left = mid + 1
         }
       }
       return ans
     }
     ```

