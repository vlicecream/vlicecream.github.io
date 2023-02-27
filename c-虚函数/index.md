# 虚函数


## 虚函数

1. ***虚函数的种类***

   - *non-virtual： 你不希望派生类重新定义*

   - *virtual：你希望派生类重新定义，且他有默认定义*

   - *pure virtual：你希望派生类一定要重新定义，你对他没有默认定义*

     ```cpp
     class Shape {
     public:
       virtual void draw() const = 0; // pure virtual
       virtual void error(const std::string &msg); // impure virtual
       int objectID() const; // non-virtual
     };
     ```

## 虚函数的实现原理

1. ***虚表与虚指针***

   - *我们先把下述代码扫一遍，并且来看看图，我们可以看到最左边的表 上面记录着 **继承的父类**和 **自己的数据**，但是除了这些之外，是不是还有一个记录着内存地址的，这个就是**虚指针（vtpr）**这个虚指针指向着一张**虚表（vtbl）***

     ```cpp
     class A {
     pubilc:
     	virtual void vfunc1();
       virtual void vfunc2();
       				void func1();
       				void func2();
     private:
       int m_data1, m_data2;
     };
     
     class B : public A {
     pubilc:
     	virtual void vfunc1();
       				void func2();
     private:
       int m_data3;
     };
     
     class C : public B {
     pubilc:
     	virtual void vfunc1();
       				void func2();
     private:
       int m_data1, m_data4;
     };
     ```

     ![虚表和虚指针](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220521213437372.png)

2. ***c++对一个虚函数进行调用的过程***

   - *他首先会考虑是**静态绑定**还是**动态绑定***
   - *静态绑定 就是 call xxx  call(汇编语言的一个动作) xxx(addr)*
   - *如果是满足三个条件就会动态绑定*
     - *通过指针来调用*
     - *指针满足向上转型*
     - *调用的是虚函数*
     - *只要满足这三个条件 就会变成 (*(p->vptr)[n])(p); 或者 (* p->vptr[n] )(p);*
     - *为什么叫动态绑定，因为要看"p"是什么*

## 虚函数的妙用

1. ***通过虚指针我们可以实现多态***

   - *比如 一个list 需要放多个不同大小的 比如 矩形 圆形 长方形 各种的 就可以直接放父类A 的指针 指向不同大小的 具体图形 因为对于指针来说 都是4个byte*

     ```cpp
     list<A*> myList;
     ```

     

