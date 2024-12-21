# LRU-Catch


# LRU Catch

## ***LRU Catch***

1. ***什么是LRU Catch***
   
   - *LRU是最近最少使用策略的缩写，是根据数据的历史访问记录来进行淘汰数据，其核心思想是“**如果数据最近被访问过，那么将来被访问的几率也更高**”*
   - *我们可以用 **双向链表**去实现 `LRU Catch`*

2. ***LRU Catch 图示***
   
   - ![LRU-Catch](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220730194248846.png)

3. ***golang 实现 LRU Catch***
   
   - ```go
     type Node struct {
         Key int
         Value int
         pre *Node
         next *Node
     }
     
     type LRUCache struct {
         limit int
         HashMap map[int]*Node
         head *Node
         end *Node
     }
     
     func Constructor(capacity int) LRUCache{
         lruCache := LRUCache{limit:capacity}
         lruCache.HashMap = make(map[int]*Node, capacity)
         return lruCache
     }
     
     func (l *LRUCache) Get(key int) int {
         if v,ok:= l.HashMap[key];ok {
             l.refreshNode(v)
             return v.Value
         }else {
             return -1
         }
     }
     
     func (l *LRUCache) Put(key int, value int) {
         if v,ok := l.HashMap[key];!ok{
             if len(l.HashMap) >= l.limit{
                 oldKey := l.removeNode(l.head)
                 delete(l.HashMap, oldKey)
             }
             node := Node{Key:key, Value:value}
             l.addNode(&node)
             l.HashMap[key] = &node
         }else {
             v.Value = value
             l.refreshNode(v)
         }
     }
     
     func (l *LRUCache) refreshNode(node *Node){
         if node == l.end {
             return
         }
         l.removeNode(node)
         l.addNode(node)
     }
     
     func (l *LRUCache) removeNode(node *Node) int{
         if node == l.end  {
             l.end = l.end.pre
             l.end.next = nil
         }else if node == l.head {
             l.head = l.head.next
             l.head.pre = nil
         }else {
             node.pre.next = node.next
             node.next.pre = node.pre
         }
         return node.Key
     }
     
     func (l *LRUCache) addNode(node *Node){
         if l.end != nil {
             l.end.next = node
             node.pre = l.end
             node.next = nil
         }
         l.end = node
         if l.head == nil {
             l.head = node
         }
     }
     ```

