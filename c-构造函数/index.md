# 构造函数


***!!!本文基于这个一个 `complex`的类去讲解 操作符重载***

```cpp
class complex {
public:
  complex(double r = 0, double i = 0) : re(r), im(i) {}
  complex& operator+=(const complex&);
  double real() const { return re; }
  double imag() const { return im; }

private:
  double re, im;

  friend complex& __doapl(complex *, const complex &);
};
```

## ***构造函数***

1. ***引出***

   - *如果你想要创建一个对象，有一个函数会自动调用起来，这个就是构造函数*

     ```cpp
     {
       complex c1(2, 1);  // 有参数的创建对象
       complex c2;  // 无参数的创建对象
       complex*p = new comolex(4);  // 动态的创建对象 是一个指针
     }
     ```

2. ***构造函数要注意点***

   - *构造函数的名称一定要跟类的名称一致*

   - *构造函数是有参数的，参数可以是为默认的*

   - *构造函数返回值是没有指定类型的，也不需要写*

   - *下述代码，`re(r)` `im(i)`是初始列，因为我们在这里将`r` `i`初始化 等同于在接下来的大括号写`{re = r; im = i;}`*

     ```cpp
     complex(double r = 0, double i = 0) : re(r), im(i) {}
     // r = 0 i = 0 都是设置了默认参数
     // re(r) im(i) 是初始列 不需要指定类型
     
     // ps:上述的一行可以写成 但是推荐上述写法 在初始列 里面将re im 初始化
     complex(double r = 0, double i = 0) { re = r; im = i; }
     ```

## ***重载***

1. ***构造函数是可以有多个的***

   - *如果你在 class body 里定义了*

     ```cpp
     double real() const {return re;}
     ```

     *又在外部定义了*

     ```cpp
     void real(double r) { re = r; }
     ```

     *这两者是 **不冲突的，因为编译器会把他们换成不一样的名字***

     *但是也有一些例子如下代码 我们要注意*

     ```cpp
     complex(double r = 0, double i = 0) : re (r), im (i) { }
     complex(): re(0), im(0) { }
     ```

     *上述两个不是不行，但是如果我们这么调用*

     ```cpp
     {
       complex c1;
       complex c2();
     }
     ```

     *这个时候他就会分不清到底要调用哪一个 complex 所以就会报错，所以切记*

## ***当构造函数在private里***

*构造函数在private里面我们无法通过正常的模式构造对象，所以一般我们是不会把构造函数放在私有区域*

***但是有一个设计模式 - 单例模式，他会把构造函数放在私有区域里***

## ***Const（常量构造函数）***

```cpp
double real () const { return re; }
// 代表数据内容 不可改变
```

## ***参数传递***

1. ***pass by value***

   - *值传递，把这个值都传过去*

     ```cpp
     complex (double r = 0, double i = 0): re (r), im (r) { }
     ```

2. ***pass by reference***

   - *指针传递，传指针，一直都会是 8byte*

     ```cpp
     Complex& operator += { const complex& }
     ```

## ***返回值传递***

1. *跟参数也是大差不差的*

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

     

2. ***相同class的各个obj互为friend***

   - *在下述代码中，我们竟然可以通过`func`直接获取到private中的值，我们也没有设置friend，这是因为相同class的各个obj互为friend*

   - ```cpp
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

