# 模版


## ***模板***

1. ***class template***
   
   - *类模板的作用就是可变类型*
     
     ```cpp
     template<typename T>
     class complex {
     public:
       complex(T r = 0, T i = 0) : re(r), im(i) {}
       complex &operator+=(const complex &);
       T real() const { return re; }
       T imag() const { return im; }
     
     private:
       T re, im;
     
       friend complex &__doapl(complex *, const complex &);
     };
     ```
     
     ```cpp
     complex<double> c1(2.5, 3.5);
     ```

2. ***func template***
   
   - *func template 也是相当于 class template*
   
   - *如果我们用stone类型去生成对象，实参推导结果：T为stone，于是调用`stone::operator <`，所以stone必须实现"<"方法*
     
     ```cpp
     class stone {
     public:
       stone(int w, int h, int we) : _w(w), _h(h), _weight(we) {}
       bool operator < (const stone& rns) const { return _weight < rhs._weight; }
     private:
       int _w, _h, _weight;
     };
     ```
     
     ```cpp
     template <class T>
     inline const T& min(const T& a, const T& b) {
         return b < a ? b : a;
     }
     ```
     
     ```cpp
     stone r1(2, 3), r2(3, 3), r3;
     r3 = min(r1, r2);
     ```

3. ***member template***
   
   - *成员模版就是我们在外面设置了T1，T2后，在里面的成员模版又可以让我们在设置一个U1，U2*
     
     ```cpp
     template <class T1, class T2>
     struct pair {
         typedef T1 first_type;
         typedef T2 second_type;
     
         T1 first;
         T2 second;
     
         pair() : first(T1()), second(T2()) { }
         pair()(const T1& a, const T2& b) : first(a), second(b) { }
     
         template <class U1, class U2>
         pair(const pair<U1, U2>& p) : first(p.first), second(p.second) { }
     };
     ```
   
   - *是不是有点听不太懂，我们这么去想 Base1 鱼类 Dervied1 鲫鱼 / Base2 鸟类 Dervied2 麻雀 我们有这4个类 对应着继承关系*
     
     ```cpp
     pair<Base1, Base2>p2<pair<Dervied1, Dervied2>()>;
     ```
     
     * 把一个由鲫鱼和麻雀组成的pair 放进(拷贝到)一个由鱼类和鸟类构成的pair中 可以吗 反之 可以嘛*
     * *正着当然可以，反之，逻辑上说不通，因为 鸟类 > 麻雀，为了显示这种逻辑，成员模板就因此而来*

## ***模板特化***

```cpp
TODO:
```

## ***模版偏特化***

1. ***个数上的偏***
   
   - ```cpp
     template <typename T, typename Alloc=...> // Alloc 分配器 
     class vector {
         ...
     };
     
     // 如果第一个typename 一直为bool 的话 为了节省空间 我们可以进行绑定优化
     
     template <typename Alloc=....> // Alloc 分配器 
     class vector<bool, Alloc> {
         ...
     };
     ```

2. ***范围上的偏***
   
   - ```cpp
     template <typename T> 
     class C {
         ...
     };
     
     template <typename T> 
     class C<T*> {
         ...
     }; // 这样的意思就是我要求编译器 如果是指针的T 就走这个 不是就走上方
     ```

## ***模板模板参数***

```cpp
TODO:
```

## ***模板的可变长参数***

```cpp
// 就是python 和 golang 的可变长参数

template <typename T, typename... Types>
void print (const T& firstArg, const Types&.. args) {
  cout << firstArg << endl;
  print(args...);
}
```

