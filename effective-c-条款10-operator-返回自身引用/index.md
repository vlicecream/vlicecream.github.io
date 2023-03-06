# 条款10-operator=返回自身引用


## operator=返回自身引用

1. *如果不反悔自身引用，那么在某些特殊场景 比如下述代码就会出现问题*

   ```cpp
   class Widget {
   public:
     void operator=(const Widget& rhs) { } // return void
   }
   
   Widget w1(1), w2(2), w3(3);
   w2 = w1 // success
   w3 = w2 = w1 // 等同于 w3 = (w2 = w1)
   ```

   *`w3 = w2 = w1` 这行先执行了 `w2 = w1` 然后 就会失败，因为传回来的是一个void 没有办法继续 `w3 = `了*

   *所以需要修改为返回自身引用，`=`要返回 其他的类似`+=` 也是同理*

   ```cpp
   class Widget {
   public:
     Widget& operator=(const Widget& ths) { return *this; }
     Widget& operator=(int rhs) { return *this; }
     Widget& operator+=(const Widget& ths) { return *this; }
   }
   ```

   ***这么做的目的就是因为要做一个统一的约定，因为`w3 = w2 = w1`确实是可以的，所以我要做成他们一样***

