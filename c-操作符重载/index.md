# 操作符重载


# 操作符重载

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

## ***操作符重载***

1. ***示例操作符重载 "+="***
   
   - *`+=`就是一个操作符，那么操作符是怎么被编译器看待的*
   
   - *下述代码 `+=` 会用到左边的`c2`上，如果`c2`有对`+=`的定义，就会调用`+=`的函数，操作符本来也就是一个函数*
     
     ```cpp
     c2 += c1;
     ```
   
   - ***我们来示范一下重载 `+=` 这个代码就是重写了`+=`函数，多包装了一层***
     
     ```cpp
     inline complex& __doapl(complex* ths, const complex& r) {
       ths->re += r.re;
       ths->im += r.im;
       return *ths; // 返回的是一个value
     }
     
     inline complex& complex::operator+=(const complex& r) {
       return __doapl(this, r);
     }
     ```

## ***成员函数 - this***

1. ***我们将上面的代码更深一层看，其实是这样的***
   
   - *这个`this`就是操作符左边的-也就是`c2`，但是在我们代码里面不可以写出来，知道他的存在就好*
     *那么`this`就一定在第一个参数吗？ 其实不一定的，但是不用考虑*
     
     ```cpp
     inline complex& complex::operator+=(this, const complex& r) {
       return __doapl(this, r);
     }
     ```

## ***Return by reference***

1. ***还是上面的代码 我们来分析分析一个问题***
   
   - *为什么 `complex *ths` 是指针的方式传进来的，return 的时候 *ths 是返回指针所指向的value，然而声明返回值的时候，说返回的是一个内存地址&*
     
     ```cpp
     inline complex& __doapl(complex* ths, const complex& r) {
       //  ...
       return *ths;
     }
     ```
   
   - *这其实是允许的，你返回一个value，接收端怎么接收，是不需要管的，所以 传递者无需知道接收者是以reference形式接收*

## ***非成员函数***

1. ***一个操作符也可以有很多个重载***
   
   - *下面三个操作符 `+` 由复数和实数组成三种，所以需要有三种全局函数*
     
     ```cpp
     {
       complex c1(2, 1);
       complex c2;
     
       c2 = c1 + c2;
       c2 = c1 + 5;
       c2 = 7 + c1;
     }
     
     inline complex operator + (const complex& x, const complex& y) {
       return complex (real(x) + real(y), imag(x) + imag(y));
     }
     
     inline complex operator + (const complex& x, double y) {
       return complex (real(x) + y, imag(x);
     }
     
     inline complex operator + (double x, const complex& y) {
       return complex (x + real(y), imag(y));
     }
     ```

## ***临时对象***

1. ***我们来看看上面 三个 `+`重载，他们绝不可return by reference***
   - *上述的 `+` 并非等同于 `+=`，`x` 和 `y`相加后的数据并没有地方放，所以在函数里一定会放在一个东西里 这里就是`complex()`*
   - *`conplex()`相当于`int()`都会创建一个临时对象，这样的意思就是这个临时对象下一行就挂了，只是临时的作用*

## ***总结***

1. ***`const`需不需要使用取决于这个值需不需要修改***
2. ***pass by reference & return by reference***
   - *一切都优先考虑 reference，如果像需要用到临时对象，则使用return by value*
   - 同时也要注意 需不需要加上const

