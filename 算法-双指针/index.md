# 双指针


## 引言

***对于数组和链表 来说 "双指针"其实是一个常用的解法了***

***双指针也是有很多种类的***

## 普通双指针

1. ***什么是双指针***
   - *双指针模式指使用两个一前一后的指针遍历数据结构，直到某个指针触发停止条件*
2. ***使用双指针的好处***
   - *单指针原本需要平方的时间复杂度，用了双指针便可优化到线性时间复杂度*

## 快慢指针

1. ***什么是快慢指针***
   - *两个速度不同的指针 比如 "fast指针" 跑两格 "slow指针" 跑一格*
   - *可以拿龟兔赛跑去想象，在一个圆形赛道，"兔子 -> 快指针" 肯定会追上 "乌龟 -> 慢指针"，龟兔赛跑也经常用在判断链表是否成环上面*

## 左右指针

1. ***来讲一讲题外话***
   - *其实 快慢指针 也有被说成 左右指针，但是本人认为这么说，其实是相当不合理，在此之上，我便有了一丝 朱光潜老先生的 "咬文嚼字" 的感觉，我始终认为 你给一个方法取名 其实就是代表了 你对这个方法的理解*
   - *比如了解什么是左右指针后 你把它叫做 "对撞指针" 也可以 这就是 "1000个读者有1000个哈姆雷特"*
   - *所以我也很推荐你们能拥有自己的想法 不要在意别人的命名 就好比 leetcode 题解 就有把快慢指针说为左右指针，你能说他错吗 其实快慢指针 也确实就是一个指针左 一个指针右 *
   
1. ***什么是左右指针***
   
   - *其实就是一个left指针从最左边向右跑，一个right指针从最右边向左跑*
   
1. ***左右指针的代码模板***
   
   - ```cpp
      int left = 0, right = vector.size()-1;
      while (left <= right) {
        ...
      }
      ```
   
   - ```go
      left, right := 0, len(array)-1
      for left <= right {
        ...
      }
      ```
   

## 滑动窗口

1. ***什么是滑动窗口***
   - *滑动窗口是一种基于双指针的一种思想，两个指针指向的元素之间形成一个窗口*
   - *其实可以把它当成一个队列 如果 窗口要向后滑动 直接把开头去掉即可~ 这只是其中一个实现思想*
2. ***滑动窗口的应用场景***
   - *滑动窗口可解决一系列字符串匹配问题*

## 双指针的应用场景

1. ***涉及 数组或链表，成对元素的集合、甚至是子数组***
2. ***匹配一个「目标」值或是去除重复***

## 例题1 - 数组 - 移动零 - 快慢指针

1. ***题目url***

   - *https://leetcode.cn/problems/move-zeroes*

2. ***思路图示***

   - ![移动0](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/images%E7%A7%BB%E5%8A%A80.png)

3. ***代码***

   - ```cpp
     class Solution {
       public:
       void moveZeroes(vector<int>& nums) {
         int slow = 0;
         for (int fast = 0; fast < nums.size(); ++fast) {
           if (nums[fast] != 0) {
             swap(nums[slow], nums[fast]);
             slow++;
           };
         }
       }
     };
     ```

   - ```go
     func moveZeroes(nums []int)  {
         // 双指针
         slow := 0
         for fast := 0; fast < len(nums); fast++ {
             // 根据具体内容写相应代码 
             if nums[fast] != 0 {
                 nums[slow], nums[fast] = nums[fast], nums[slow]
                 slow++
             }
         }
     }
     ```

4. ***其他例题***

   - *https://leetcode.cn/problems/remove-duplicates-from-sorted-array/*
   - *https://leetcode.cn/problems/merge-sorted-array/*

## 例题2 - 数组 - 盛最多水的容器 - 左右指针

1. ***题目url***

   - *https://leetcode.cn/problems/container-with-most-water/*

2. ***解题思路***

   - *这道题是 "求最优解" 其实求最优解 是 "贪心算法" 和 "动态规划" 的场景 但是这里也可以用双指针*

3. ***思维图示***

   - ![盛最多水的容器](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/images%E7%9B%9B%E6%9C%80%E5%A4%9A%E6%B0%B4%E7%9A%84%E5%AE%B9%E5%99%A8.png)

4. ***题解代码***

   - ```cpp
     class Solution {
     public:
         int maxArea(vector<int>& height) {
             if (height.size() == 0) return 0;
             // 双指针 -- 左右指针
             int left = 0, right = height.size()-1, area = 0;
             while (left < right) {
                 if (min(height[left], height[right]) * (right-left) > area) area = min(height[left], height[right]) * (right-left);
                 if (height[left] <= height[right]) ++left;
                 else --right;
             }
             return area;
         }
     };
     ```

   - ```go
     func maxArea(height []int) int {
        left, right, area := 0, len(height)-1, 0
        for left < right {
           area = max(area, min(height[left], height[right])*(right-left))
           if height[left] < height[right] {
          	  left++
           } else {
        	    right--
           }
        }
        return area
     }
     
     func max(x, y int) int {
        if x > y {
        	 return x
        } else {
        	 return y
        }
     }
     
     func min(x, y int) int {
        if x < y {
        	 return x
        } else {
        	 return y
        }
     }
     ```

5. ***其他例题***

   - *https://leetcode.cn/problems/rotate-array/*

## 例题3 - 链表 -  成环链表 - 快慢指针

1. ***题目url***

   - *https://leetcode.cn/problems/linked-list-cycle/*

2. ***解题思路***

   - *成环链表 用 "快慢指针" 真的人人皆知*
   - *所以也要留个心眼 学个其他的解法 面试的时候 也是个亮点哦*

3. ***思维图示***

   - ![成环链表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/images%E6%88%90%E7%8E%AF%E9%93%BE%E8%A1%A8.png)

4. ***题解代码***

   - ```cpp
     class Solution {
     public:
         bool hasCycle(ListNode *head) {
             if (head == NULL || head->next == NULL) return false;
     
             ListNode* slow = head, *fast = head->next;
             while (slow != fast) {
                 if (fast == NULL || fast->next == NULL) return false;
     
                 slow = slow->next;
                 fast = fast->next->next;
             }
             return true;
         }
     };
     ```

   - ```go
     func hasCycle(head *ListNode) bool {
         if head == nil || head.Next == nil {
             return false
         }
     
         slow, fast := head, head.Next
         
         for slow != fast {
             if fast == nil || fast.Next == nil {
                 return false
             }
             slow = slow.Next
             fast = fast.Next.Next
         }
         return true
     }
     ```

5. ***其他例题***

   - *https://leetcode.cn/problems/linked-list-cycle-ii*

## 例题4 - 链表 -  反转链表 - 双指针

1. ***题目url***
   - *https://leetcode.cn/problems/reverse-linked-list/*

2. ***解题思路***
   - *这道题我们可以这么看 **1 -> 2 -> null** 然后变成 **null <- 2 <- 1***
   - *在遍历链表时，将当前节点的 next 指针改为指向前一个节点，由于节点没有引用其前一个节点，因此必须事先存储其前一个节点，在更改引用之前，还需要存储后一个节点。最后返回新的头引用*

3. ***题解代码***

   - ```cpp
      class Solution {
      public:
          ListNode* reverseList(ListNode* head) {
              ListNode* prev = nullptr, *curr = head;
              while (curr != nullptr) {
                  ListNode* next = curr->next;
                  curr->next = prev;
                  prev = curr;
                  curr = next;
              }
              return prev;
          }
      };
      ```

   - ```go
      func reverseList(head *ListNode) *ListNode {
          var prev *ListNode
          curr := head
          for curr != nil {
              next := curr.Next
              curr.Next = prev
              prev = curr
              curr = next
          }
          return prev
      }
      ```


## 例题5 - 字符串 - 无重复字符的最长字串

1. ***题目url***
   - *https://leetcode.cn/problems/longest-substring-without-repeating-characters/*




