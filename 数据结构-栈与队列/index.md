# 栈&队列


## ***什么是队列***

- *队列是一个 **先进先出** 的数据结构*
- *先进来的元素 先被消费使用*
- *好比排队 第一个排队的人肯定第一个出来 这就是先进先出*
- *队列有两种重要的方法 - push（往队列的尾部塞入元素）/ pop（往队列的头部删除元素）*

## ***什么是栈***

- *栈是一个 **先进后出** 的数据结构*
- *先进来的元素 最后一个被消费使用 / 最后进来的元素 第一个被消费使用*
- *好比一个箱子 你往里面放书 第一个书本在最下面 你只能第一时间拿出最上面的书 也就是最后放进去的书*
- *栈有两种重要的方法 - push（往栈的尾部塞入元素）/ pop（往栈的尾部删除元素）*

## ***什么是双端队列***

- *是一种具有队列和栈性质的数据类型*
- *双端队列中的元素可以从两端弹出，插入和删除操作限定在队列的两边进行*

## ***什么是优先级队列***

- *依靠每个元素的优先级 来决定哪一个最先被消费使用*

## ***Golang实现队列***

***golang 如何实现队列 及其 他的时间复杂度是多少***

- ***insert：O(1)***

- ***delete: O(1)***

- ```go
  // A FIFO queue.
  type Queue []int
  
  // 往队列的尾部塞入元素
  func (q *Queue) Push(v int) {
      *q = append(*q, v)
  }
  
  //  pop（往队列的头部删除元素）
  func (q *Queue) Pop() int {
      head := (*q)[0]
      *q = (*q)[1:]
      return head
  }
  
  // Returns if the queue is empty or not.
  func (q *Queue) IsEmpty() bool {
      return len(*q) == 0
  }
  ```

## ***Golang实现栈***

***golang 如何实现栈 及其他的时间复杂度是多少***

- ***insert: O(1)***

- ***delete: O(1)***

- ```go
  type Stack []interfice{}
  
  func (stack Stack) Len() int {
      return len(stack)
  }
  
  func (stack Stack) Cap() int {
      return cap(stack)
  }
  
  func (stack *Stack) Push(value interface{})  {
      *stack = append(*stack, value)
  }
  
  func (stack Stack) Top() (interface{}, error)  {
      if len(stack) == 0 {
          return nil, errors.New("Out of index, len is 0")
      }
      return stack[len(stack) - 1], nil
  }
  
  func (stack *Stack) Pop() (interface{}, error)  {
      theStack := *stack
      if len(theStack) == 0 {
          return nil, errors.New("Out of index, len is 0")
      }
      value := theStack[len(theStack) - 1]
      *stack = theStack[:len(theStack) - 1]
      return value, nil
  }
  
  func (stack Stack) IsEmpty() bool  {
      return len(stack) == 0
  }
  ```

## ***Tips***

***不要仅限于固定思想 实现栈 和 队列的方式千千万万***

- *用两个栈实现队列*
- ***https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/***

