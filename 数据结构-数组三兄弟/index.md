# 数组-链表-跳表


# 数组-链表-跳表

## ***数组***

1. ***数组的特点***
   - *当我们申请数组的时候，计算机会在内存开辟一段连续的内存地址，内存管理器可以直接访问每个内存地址，所以我们可以通过" [  ] "去取值*
2. ***数组的时间复杂度***
   - ***select：*** *O(1)*
   - ***insert：*** *O(n)*
   - ***append：*** *O(1)*
   - ***delete：*** *O(n)*
3. ***数组时间复杂度图示***
   - ![数组 时间复杂度](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302251145620.jpg)

## ***链表***

1. ***引出***
   
   - *研究数据结构的科学家们 看到数组的 "insert" "delete" 就会去想优化这个时间复杂度 随后 链表诞生了*

2. ***何为链表***
   
   - *非连续的内存空间 有一个个的* ***节点（node）*** *组成*
   - *node 又是由 **value next** 组成*
   - *value 可以为int string  对象 ... 等类型*
   - *next 则就是一个指针 指向下一个node*

3. ***时间复杂度***
   
   - ***select:*** *O(n)*
   - ***Append:*** *O(1)*
   - ***insert:*** *O(1)*
   - ***delete:*** *O(1)*

4. ***ps: 不结合实际场景的计算时间复杂度都是耍流氓***

5. ***图示***
   
   ![链表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202310232157907.jpg)

## ***单向链表***

1. ***时间复杂度***
   
   - *select: O(n)  查询需要从head节点遍历 所以 O(n)*
   - *insert: O(n)  插入需要从head节点遍历到 插入的前驱节点 所以O(n)*
   - *delete: O(n) 删除跟插入同理*

2. ***golang 实现单向链表***
   
   - ```go
     type Object interface{}
     
     type Node struct {
         Data Object
         next *Node
     }
     
     type List struct {
         size uint64
         head *Node
         tail *Node
     }
     
     func (list *List) Init() {
         (*list).size = 0
         (*list).head = nil
         (*list).tail = nil
     }
     
     // 向链表追加节点
     func (list *List) Append(node *Node) bool {
         if node == nil {
             return false
         }
     
         (*node).next = nil // 新加节点在末尾，没有next
         if (*list).size == 0 {
             (*list).head = node
         } else {
             oldTail := (*list).tail // 取尾结点
             (*oldTail).next = node  // 尾结点的next指向新加节点
         }
     
         (*list).tail = node // 新节点是尾结点
         (*list).size++
         return true
     }
     
     // 向第i个节点处插入节点
     func (list *List) Insert(i uint64, node *Node) bool {
         if node == nil || i > (*list).size || (*list).size == 0 {
             return false
         }
     
         if i == 0 {
             (*node).next = (*list).head
             (*list).head = node
         } else {
             preNode := (*list).head
             for j := uint64(1); j < i; j++ {
                 preNode = (*preNode).next
             }
     
             (*node).next = (*preNode).next // 新节点指向旧节点原来所指的next
             (*preNode).next = node         // 原节点的next指向新节点
         }
         (*list).size++
     
         return true
     }
     
     // 移除指定位置的节点
     func (list *List) Remove(i uint64) bool {
         if i >= (*list).size {
             return false
         }
     
         if i == 0 {
             preHead := (*list).head     // 取出旧的链表头
             (*list).head = preHead.next // 旧链表头的next变为新的头
     
             // 如果仅有一个节点，则头尾节点清空
             if (*list).size == 1 {
                 (*list).head = nil
                 (*list).tail = nil
             }
         } else {
             preNode := (*list).head
             for j := uint64(1); j < i; j++ {
                 preNode = (*preNode).next
             }
     
             node := (*preNode).next     // 找到当前要删除的节点
             (*preNode).next = node.next // 把当前要删除节点的next赋给其父节点的next,完成后代转移
     
             // 若删除的尾部，尾部指针需要调整
             if i == ((*list).size - 1) {
                 (*list).tail = preNode
             }
         }
     
         (*list).size--
     
         return true
     }
     
     // 移除所有节点
     func (list *List) RemoveAll() bool {
         (*list).Init()
         return true
     }
     
     // 获取指定位置的节点
     func (list *List) Get(i uint64) *Node {
         if i >= (*list).size {
             return nil
         }
     
         node := (*list).head
         for j := uint64(0); j < i; j++ {
             node = (*node).next
         }
     
         return node
     }
     
     // 搜索某个数据的节点位置
     func (list *List) IndexOf(data Object) int64 {
         pos := int64(-1)
         node := (*list).head
         if node.Data == data {
             return 0
         }
     
         for j := uint64(1); j < (*list).size; j++ {
             if node != nil {
                 node = (*node).next
                 if node != nil && node.Data == data {
                     pos = int64(j)
                     break
                 }
             }
         }
         return pos
     }
     
     // 取得链表长度
     func (list *List) GetSize() uint64 {
         return (*list).size
     }
     
     // 取得链表头
     func (list *List) GetHead() *Node {
         return (*list).head
     }
     
     // 取得链表尾
     func (list *List) GetTail() *Node {
         return (*list).tail
     }
     ```

## ***双向链表***

1. ***时间复杂度***
   
   - *select: O(n)  查询需要从head节点遍历 所以 O(n)*
   - *insert: O(n) 跟单项链表一样 都需要知道前驱node*
   - *delete: O(n) 同上*
   - *append: O(1)*

2. ***golang 实现代码***
   
   ```go
   // 节点数据
   type DoubleObject interface{}
   
   // 双链表节点
   type DoubleNode struct {
       Data DoubleObject
       Prev *DoubleNode
       Next *DoubleNode
   }
   
   // 双链表
   type DoubleList struct{
       mutex *sync.RWMutex
       Size uint
       Head *DoubleNode
       Tail *DoubleNode
   }
   
   // 双链表初始化
   func (list *DoubleList)Init()  {
       list.mutex = new(sync.RWMutex)
       list.Size = 0
       list.Head = nil
       list.Tail = nil
   }
   
   // Get 获取指定位置的节点
   func (list *DoubleList)Get(index uint) *DoubleNode {
       if list.Size == 0 || index > list.Size - 1 {
           return nil
       }
       if index == 0{
           return list.Head
       }
       node := list.Head
       var i uint
       for i = 1; i <= index; i ++{
           node = node.Next
       }
       return node
   }
   
   // Append 向双链表后面追加节点
   func (list *DoubleList)Append(node *DoubleNode) bool {
       if node == nil{
           return false
       }
       list.mutex.Lock()
       defer list.mutex.Unlock()
       if list.Size == 0 {
           list.Head = node
           list.Tail = node
           node.Next = nil
           node.Prev = nil
       } else {
           node.Prev = list.Tail
           node.Next = nil
           list.Tail.Next = node
           list.Tail = node
       }
       list.Size++
       return true
   }
   
   // Insert 向双链表指定位置插入节点
   func (list *DoubleList)Insert(index uint, node *DoubleNode) bool {
       if index > list.Size || node == nil{
           return false
       }
   
       if index == list.Size{
           return list.Append(node)
       }
   
       list.mutex.Lock()
       defer list.mutex.Unlock()
       if index == 0{
           node.Next = list.Head
           list.Head = node
           list.Head.Prev = nil
           list.Size++
           return true
       }
   
       nextNode := list.Get(index)
       node.Prev = nextNode.Prev
       node.Next = nextNode
       nextNode.Prev.Next = node
       nextNode.Prev = node
       list.Size++
       return true
   }
   
   // Delete 删除指定位置的节点
   func (list *DoubleList) Delete (index uint) bool {
       if index > list.Size - 1 {
           return false
       }
   
       list.mutex.Lock()
       defer list.mutex.Unlock()
       if index == 0 {
           if list.Size == 1{
               list.Head = nil
               list.Tail = nil
           } else {
               list.Head.Next.Prev = nil
               list.Head = list.Head.Next
           }
           list.Size--
           return true
       }
       if index == list.Size - 1{
           list.Tail.Prev.Next = nil
           list.Tail = list.Tail.Prev
           list.Size--
           return true
       }
   
       node := list.Get(index)
       node.Prev.Next = node.Next
       node.Next.Prev = node.Prev
       list.Size--
       return true
   }
   ```

## ***跳表***

1. ***引出***
   
   - *对于一组有序数据，我们想要在其中查找某个数，如果数据使用数组存储，显然二分法是再适合不过了，但是如果数据是用链表存储的呢？难道我们用从头遍历吗？这样时间复杂度会达到O（n）级别，相比二分法O（logn）级别简直天壤地别。那么如何提高效率呢*

2. ***跳表***
   
   - *算法有两个很重要的思想*
     - *空间换时间*
     - *升维*
   - *跳表其实就是链表采用了升维的思想*

![原始链表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242345644.png)

*如下图，对初始链表做一层“索引”，每两个节点提取一个节点到上一层，然后用down指针连接到下一层。*

![跳表 - 一级索引](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242347419.png)*现在我们查询16这个节点。从第一级索引开始，找到13，并且下一个为17，显然16在这两个节点之间，利用down指针下降一层，这次我们遍历2次即可。利用索引后，遍历了5+2=7次，而原始链表需要10次，这里加一层索引遍历次数减少了，效率提高了一点，但还不够，我们继续往上添加索引层*

![跳表 - 二级索引](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202302242346672.png)

