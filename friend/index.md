# 

# friend


## ***友元***

1. ***什么是友元***

   - *Friend 的意义在于打开封装的大门，可以直接从私有区域拿取数据*

   - *好比借钱，陌生人不可能借钱给他，但是朋友是可以的，但是朋友太多借太多钱也不太好，所以你自己选择，如果不设置friend，绕个弯从函数拿，也是可以的*

     ```cpp
     private:
     	double re, im;
     	friend complex& __dopal (conplex*, const complex&)
         
     inline complex& __doapl (complex* ths, const complex& r) {
       ths->re += r.re;
       ths->im += r.im;
       return *ths
     }
     ```

## ***友元的使用场景***

1. *普通函数定义为类的友元函数，使得普通函数能够访问该类的私有成员和保护成员*

   ```cpp
   #include <iostream>
   
   using namespace std;
   
   class A
   {
       friend ostream &operator<<(ostream &_cout, const A &tmp); // 声明为类的友元函数
   
   public:
       A(int tmp) : var(tmp)
       {
       }
   
   private:
       int var;
   };
   
   ostream &operator<<(ostream &_cout, const A &tmp)
   {
       _cout << tmp.var;
       return _cout;
   }
   
   int main()
   {
       A ex(4);
       cout << ex << endl; // 4
       return 0;
   }
   ```

2. *友元类*

   *由于类的 `private` 和` protected` 成员变量只能由类的成员函数访问或者派生类访问，友元类则提供提供一种通用的方法，使得不同类之间可以访问其 `private` 和 `protected` 成员变量，用于不同类之间共享数据*

   ```cpp
   #include <iostream>
   
   using namespace std;
   
   class A {
       friend class B;
   
   public:
       A() : var(10){}
       A(int tmp) : var(tmp) {}
       void fun()
       {
           cout << "fun():" << var << endl;
       }
   
   private:
       int var;
   };
   
   class B {
   public:
       B() {}
       void fun() {
           cout << "fun():" << ex.var << endl; // 访问类 A 中的私有成员
       }
   private:
       A ex;
   };
   
   int main() {
       B ex;
       ex.fun(); // fun():10
       return 0;
   }
   ```

## ***相同class的各个obj互为friend***

*在下述代码中，我们竟然可以通过`func`直接获取到private中的值，我们也没有设置friend，这是因为相同class的各个obj互为friend*

```cpp
class complex {
public:
  complex(double r = 0, double i = 0) : re(r), im(i) {}
  
  int func(const complex& param) { return param.re + param.im; }

private:
  double re, im;
};

{
  complex c1(2, 1);
  complex c2;
  
  c2.func(c1);
}
```



