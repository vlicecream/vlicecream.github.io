# 链表


# 例题

## 例题1 -  成环链表

1. ***题目url***

   - *https://leetcode.cn/problems/linked-list-cycle/*

2. ***方法1 - 快慢指针***

   - ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/images%E6%88%90%E7%8E%AF%E9%93%BE%E8%A1%A8.png)

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

3. ***其他例题***

   - *https://leetcode.cn/problems/linked-list-cycle-ii*

## 例题2 - 反转链表

1. ***题目url***

   - *https://leetcode.cn/problems/reverse-linked-list/*

2. ***方法1 - 利用中间状态***

   - *这道题让我们反转链表，1->2->3->4->5->NULL，5->4->3->2->1->NULL，我们可以利用三个node 实现这个反转效果*

     ```cpp
     class Solution {
     public:
         ListNode* reverseList(ListNode* head) {
             ListNode* perv = nullptr, *curr = head;
             while (curr != nullptr) {
                 ListNode* next = curr->next;
                 curr->next = perv;
                 perv = curr;
                 curr = next;
             }
             return perv;
         }
     };
     ```

## 例题3 - 合并两个有序链表

1. ***题目url***

   - *xxx*

2. ***方法1 - 递归***

   - ***tips: 这里递归是需要递归基础的 我有一篇博客 "4步让你学会递归" 请先移步 学会再回来 递归其实不难***

   - *这道题就可以用递归去实现，让我们结合代码来分析 4个if*

   - *第一个 if list1 这个node如果为空 就可直接返回 list2 这个节点*

   - *第二个 跟 第一个一样的道理*

   - *第三个 就是如果list1的值比list2小 就进入到下一层 并将比较小的list1返回出来*

   - *第四个 跟第三个同理*

     ```cpp
     class Solution {
     public:
         ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
             if (!list1) return list2;
             else if (!list2) return list1;
             else if (list1->val < list2->val) {
                 list1->next = mergeTwoLists(list1->next, list2);
                 return list1;
             }
             else {
                 list2->next = mergeTwoLists(list1, list2->next);
                 return list2;
             }
         }
     };
     ```

## 例题4 - 移除重复节点

1. ***题目url***

   - *https://leetcode.cn/problems/remove-duplicate-node-lcci/?favorite=xb9lfcwi*

2. ***方法1 - set***

   - *我们可以用set来当临时缓冲区 来去除一些重复的节点，这道题说了一个 不用临时缓冲区 不要考虑这个了，看了下官方题解 就是暴力解法 时间换空间 很蠢！ 我们只会在不影响时间的情况下 去优化方法 节约空间*

     ```cpp
     class Solution {
     public:
         ListNode* removeDuplicateNodes(ListNode* head) {
             if (head == nullptr) return nullptr;
             unordered_set<int> occurred = {head->val};
             ListNode* pos = head ;
             while (pos->next != nullptr) {
                 ListNode* cur = pos->next;
                 if (!occurred.count(cur->val)) {
                     occurred.insert(cur->val);
                     pos = pos->next;
                 }
                 else {
                     pos->next = pos->next->next;
                 }
             }
             pos->next = nullptr;
             return head;
         }
     };
     ```

## 例题5 - 返回倒数第k个节点

1. ***题目url***

   - *https://leetcode.cn/problems/kth-node-from-end-of-list-lcci/*

2. ***方法1 - array(额外的数据结构)***

   - *我们遍历link-list的node 存放到array中 随后直接根据k值取就好了*

     ```cpp
     class Solution {
     public:
         int kthToLast(ListNode* head, int k) {
             if (head == nullptr) return -1;
             vector<int> res;
             while (head != nullptr) {
                 res.push_back(head->val);
                 head = head->next;
             }
             for (int i = res.size()-1; i >= 0 ; --i) {
                 if (i == res.size()-k) return res[i];
             }
             return 0;
         }
     };
     ```

3. ***方法2 - 快慢指针(优化了空间复杂度)***

   - *我们初始化两个指针变量为head节点 - slow fast*

   - *让fast先跑k个节点*

   - *随后 slow&fast 每次都跑一个node*

   - *fast == 空的时候 slow所在node就是我们要的node了*

     ```cpp
     class Solution {
     public:
         int kthToLast(ListNode* head, int k) {
             if (head == nullptr) return -1;
             ListNode* slow = head, *fast = head;
             // 让快指针先跑
             for (int i = 0; i < k; ++i) {
                 fast = fast->next;
             }
             // 随后一块动起来
             while (fast != nullptr) {
                 slow = slow->next;
                 fast = fast->next;
             }
             return slow->val;
         }
     };
     ```

