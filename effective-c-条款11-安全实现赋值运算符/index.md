# 条款11-安全实现赋值运算符


## ***潜在的自我赋值***

*自我赋值一个bug：如果类里面有动态内存分配，那么在赋值的时候，需要先delete掉原来的，再new一个新的，最后赋值。但如果是自我赋值，那么在delete掉原来的内存的同时，需要赋的值也被delete了（因为都是同一块内存）*

- *下面是个例子，假设我们有一个Bitmap类，一个Widget类。其中Widget有一个Bitmap的指针*

  ```cpp
  class Bitmap{};
  class Widget {
  public:
      Widget& operator=(const Widget& rhs);
  private:
      Bitmap* pb;
  };
  
  // 防止自我赋值的
  Widget& Widget::operator=(const Widget& rhs){
      delete pb;
      pb = new Bitmap(*rhs.pb);
      return *this
  }
  ```

*解决这个问题的方法就是在前面加个判断*

- ```cpp
  class Bitmap{};
  class Widget {
  public:
      Widget& operator=(const Widget& rhs);
  private:
      Bitmap* pb;
  };
  
  // 防止自我赋值的
  Widget& Widget::operator=(const Widget& rhs){
      if (this == *rhs) return *this;
      delete pb;
      pb = new Bitmap(*rhs.pb);
      return *this
  }
  ```

*除此之外还有个问题。如果赋值的时候，new一块新的空间失败了，那么pb会指向一块被delete掉的空间。这样的指针是有害的*

- ```cpp
  Widget& Widget::operator=(const Widget& rhs) {
      Bitmap *tmp = pb;
      pb = new Bitmap(*rhs.pb);
      delete tmp;
      return *this;
  }
  ```

  *首先上面这个版本申请了一个临时变量保存原始的对象。然后new一个Bitmap并赋值。如果这里出错了，还没到delete，其他的所有东西都保持原样。如果没有出错，则再将原始的空间，通过这个临时变量delete。这就解决了上面的问题*

  *然后它还取消了自我赋值的检测。但是他依然可以处理自我检测问题，假如两个指针指向同一个对象，它也会先创建一个新的副本，赋值以后再删除原来的版本*

## ***copy and swap技术***

*这个技术需要保证swap函数是异常安全的，具体后面的条款会解释，这里只做了解*

- ```cpp
  Widget& Widget::operator=(const Widget& rhs) {
      Widget tmp(rhs);
      swap(tmp);
      return this;
  }
  ```

