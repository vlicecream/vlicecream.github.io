# 二分查找


# ***二分查找***

## ***二分查找的思想-减而治之***

1. *二分查找通过不断缩小区间的范围来查找目标元素，这种"不断缩小区间"就是减而治之的思想*

2. *减而治之听起来高大上，其实就是排除法，我们每一次查找就排除掉一些元素，每一次重复此操作，自然就找到了我们的目标元素*

## ***二分查找的应用范围***

1. *在有序数组中寻找一个目标值，有序和数组是重点*

2. *在整数范围内寻找一个目标值，也就是不一定是有序数组，旋转数组和山脉数组都可以用到二分法*

## ***二分查找的算法思路***

### ***在循环体中查找元素***

1. *简介*
   
   - *此思路很简单，就是直接在数组中查找到目标元素*
   - *时间复杂度 O(log N)，这里的N是指数组的长度*
   - *空间复杂度：由于二分查找算法在执行的过程中只使用到常数个临时变量，因此空间复杂度是 O(1)。*

2. *代码模板*
   
   ```cpp
   int search(vector<int>& nums, int target) {
     int left = 0, right = nums.size()-1;
     while (left <= right) {
         int mid = left + (right - left) / 2;
         if (nums[mid] == target) {
             return mid;
         }
         if (nums[mid] < target) {
             left = mid + 1;
         }
         if (nums[mid] > target) {
             right = mid - 1;
         }
     }
   }
   ```

### ***在循环体中排除目标元素一定不存在的区间***

1. *简介*
   
   - *这种思路就是一直排除元素不存在的区间，然后剩下来最后一个数 也就是`left = right`的数，因为这里的循环条件就是`while (left < right) {}`，最后进行这个数的判断是不是我们的目标数*
   - *时间复杂度 O(log N)，这里的N是指数组的长度*
   - *空间复杂度：由于二分查找算法在执行的过程中只使用到常数个临时变量，因此空间复杂度是 O(1)。*

2. *代码模板*
   
   ```cpp
   int search(vector<int>& nums, int target) {
     int left = 0, right = nums.size()-1;
     while (left < right) {
         int mid = left + (right - left) / 2;
         if (nums[mid] == target) {
             return mid;
         }
         if (nums[mid] < target) {
             left = mid + 1;
         }
         if (nums[mid] > target) {
             right = mid - 1;
         }
     }
   }
   ```

## ***二分查找的重点***

### ***循环条件***

*循环条件是一个很重要的东西，我们到底需要用`left <= right` 还是使用`left < right`，其实这就是跟思路有关了*

### ***取中间数的代码***

*一般来说我们取中间数都是用`(left + right) / 2`这么使用如果两个数特别大的时候会造成溢出的，所以我们不能这么用*

*最好的我们是使用`left + (right - left) / 2`，这个计算方法很好的避免了溢出这个问题，故二分法要这么去写*

*其实还有一个利用位运算很帅的一个方法，`int mid = (left + right) >> 1;` 比如这个代码，大家做个了解即可这是因为整数右移 1 位和除以 2（向下取整）是等价的，这样写的原因是因为位运算比整除运算要快一点。但事实上，高级的编程语言，对于 / 2 和除以 2 的方幂的时候，在底层都会转化成为位运算，我们作为程序员在编码的时候没有必要这么做，就写我们这个逻辑本来要表达的意思即可，这种位运算的写法，在 C++ 代码里可能还需要注意优先级的问题*

### ***中间数的代码要不要向上取整***

*`int mid = left + (right - left) / 2`中`/ 2`表示的含义其实是向下取整，如果最后区间位于中间的有两位数，那么只能取到左边的数，那么上取整和下取整有没有啥区别呢，其实是有的，这种区别我们可以在具体的题目中去感悟，哪一个*

## ***案例一 - [二分查找](https://leetcode.cn/problems/binary-search/description/)***

1. *在循环体中查找元素*
   
   ```cpp
   class Solution {
   public:
   int search(vector<int>& nums, int target) {
     int left = 0, right = nums.size()-1;
     while (left <= right) {
         int mid = (left + right) / 2;
         if (nums[mid] == target) {
             return mid;
         }
         if (nums[mid] < target) {
             left = mid + 1;
         }
         if (nums[mid] > target) {
             right = mid - 1;
         }
     }
     return -1;
   }
   };
   ```

2. *在循环体中排除目标元素一定不存在区间*
   
   ```cpp
   class Solution {
   public:
     int search(vector<int>& nums, int target) {
         int left = 0, right = nums.size()-1;
         while (left < right) {
             int mid = (left + right) / 2;
             if (nums[mid] < target) {
                 left = mid + 1;
             }
             else {
                 right = mid;
             }
         }
         if (nums[left] == target) {
             return left;
         }
         return -1;
     }
   };
   ```

## ***案例二 - [猜数字大小](https://leetcode.cn/problems/guess-number-higher-or-lower/)***

1. *在循环体中查找元素*
   
   ```cpp
   class Solution {
   public:
     int guessNumber(int n) {
         int left = 1, right = n;
         while (left <= right) {
             int mid = left + (right - left) / 2;
             if (0 == guess(mid)) {
                 return mid;
             }
             if (-1 == guess(mid)) {
                 right = mid - 1;
             }
             if (1 == guess(mid)) {
                 left = mid + 1;
             }
         }
   
         return -1;
     }
   };
   ```

2. *在循环体中排除目标元素一定不存在区间*
   
   ```cpp
   class Solution {
   public:
     int guessNumber(int n) {
         int left = 1, right = n;
         while (left < right) {
             int mid = left + (right - left) / 2;
             if (1 == guess(mid)) {
                 left = mid + 1;
             }
             else {
                 right = mid;
             }
         }
         return left;
     }
   };
   ```

# ***课后习题***

1. *搜索插入位置*

2. *在排序数组中查找元素的第一个和最后一个位置*

3. *寻找旋转排序数组中的最小值*

4. *寻找旋转排序数组中的最小值 II*

5. *搜索旋转排序数组*

6. *搜索旋转排序数组 II*

7. *第一个错误的版本*

8. *山脉数组的峰顶索引*

9. *山脉数组中查找目标值*

10. *寻找两个正序数组的中位数*

11. *x 的平方根*

12. *寻找重复数*

13. *转变数组后最接近目标值的数组和*

14. *爱吃香蕉的珂珂*

15. *分割数组的最大值*

16. *在 D 天内送达包裹的能力*

17. *制作 m 束花所需的最少天数*

18. *小张刷题计划*

