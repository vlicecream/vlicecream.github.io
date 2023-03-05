# 条款7-为多态基类声明virtual析构函数


## ***为多态基类声明virtual析构函数***

1. *如果不为多态基类声明`virtual`析构函数，那么在实例化派生类的时候，则会只调用基类的析构函数，就会造成内存泄漏*

   ```cpp
   class TimeKeeper {
   public:
     TimeKeeper();
     ~TimeKeeper(); // non-virtual
   };
   
   class AtomicClock : public TimeKeeper { };
   
   // getTimeKeeper(); 工厂函数，有可能实例化了AtomicClock，所以这时候一个父类的指针指向了子类
   TimeKeeper* ptk = getTimeKeeper();
   ... // use it
   delete ptk; // 这个就会出现问题，因为TimeKeeper的析构函数不是虚函数，所以只会用了TimeKeeper基类的析构函数
   ```

## ***不是为了多态特性，不要随意声明virtual析构函数***

1. *如果不需要多态特性，还会析构函数声明了`virtual`，那么析构函数会变大，多了虚指针和虚表*

## ***类继承时小心父类析构函数不具有多态特性***

