# 组合与继承


## ***Composition 组合***

1. ***什么是组合及实现***
   
   - *组合就是一个类里面用了另一个类，并且可以用另一个类的方法*
     
     ```cpp
     template <class T, class Sequence = deque<T>>
     class queue {
       ...
     protected:
       Sequence c;  // 底层容器
     public:
       // 以下完全利用 c 的操作函数完成
       bool empty() const { return c.empty(); }
       size_type size() const { retiurn c.size(); }
       reference front() { return c.front(); }
       // deque 是两端可进出 queue 是先进先出
       void push(const value_type &x) { c.push_back(x); }
       void pop() { c.pop_front(); }
     }
     ```
   
   - *组合其实是一个最容易被忽视的东西，设计模式的桥模式跟装饰模式组合是一个最重要的思路*

2. ***组合下的构造和析构***
   
   - ***比如 Container 类里面调用了 Component***
   - *Container的构造函数首先调用Component的default构造函数然后才执行自己（从内到外）*
   - *Container的析构函数首先执行自己 然后才调用Component的析构函数（从外到内）*

## ***Inheritance 继承***

1. ***什么是继承***
   
   - *所谓的继承就是一个类继承了另一个类的属性和方法，这个新的类包含了上一个类的属性和方法，被称为子类或者派生类，被继承的类称为父类或者基类*
   
   - *子类拥有父类的所有属性和方法，子类可以拥有父类没有的属性和方法，子类对象可以当做父类对象使用*
     
     ```cpp
     struct _List_node_base {
       _List_node_base* _M_next;
       _List_node_base* _M_prev;
     };
     
     template<typename _Tp>
     struct _List_node : public _List_node_base {
       _Tp _m_data;
     };
     
     // 父类的数据 子类都可以继承下来
     ```

2. ***继承下的构造和析构***
   
   - ***Base: 父类 / Derived: 派生类***
   - *Derived 的构造函数首先调用Base的default构造函数 然后才执行自己（从内到外）*
   - *Derived 的析构函数首先执行自己, 然后才调用Base的析构函数（从外到内）*
   - ***注意: base class 的 dtor 必须是virtual 否则会出现 underfined behavior***

## ***组合&继承的构造和析构***

1. ***Base: 父类 / Derived: 派生类***
   - *Derived 的构造函数 首先调用Base的default构造函数，然后调用Component的default构造函数，然后才执行自己*
   - *Derived 的析构函数首先执行自己，然后调用Component的析构函数，然后调用Base的析构函数*

