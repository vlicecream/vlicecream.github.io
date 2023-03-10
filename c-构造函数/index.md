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

## ***所有的构造函数***

1. ***无参构造函数***
   - *即默认构造函数，如果没有明确写出无参数构造函数，编译器会自动生成默认的无参数构造函数，函数为空，什么也不做，如果不想使用自动生成的无参构造函数，必需要自己显示写出一个无参构造函数*
2. ***有参构造函数***
   - *就是有参数的构造函数*
3. ***拷贝构造函数***
   - *拷⻉构造函数的函数参数为对象本身的引用，用于根据一个已存在的对象复制出一个新的该类的对象，一般在函数中会将已存在的对象的数据成员的值一一复制到新创建的对象中。如果没有显示的写拷⻉构造函数，则系统会默认创建一个拷⻉构造函数，但当类中有指针成员时，最好不要使用编译器提供的默认的拷⻉构造函数，最好自己定义并且在函数中执行深拷⻉*
4. ***转换构造函数***
   - *根据一个指定类型的对象创建一个本类的对象，也可以算是一般构造函数 的一种*
   - *这里提出来，是想说有的时候不允许默认转换的话，要记得将其声明为 explict 的，来阻止一些隐式转换的发生*

## ***转换构造函数***

1. ***什么是转换函数***

   - *`operator double ()`这就是转换函数*

     ```cpp
     class Fraction {
     public:
       Fraction(int num, int den=1):m_numerator(num), m_denominator(den) { }
       operator double() const {
         return (double)(m_numerator / m_denominator);
       }  // 因为转换完就是 double，所以不需要写 typename，然后因为不会改变这个元素，所以也加了const
     private:
       int m_numerator; // 分子
       int m_denominator; // 分母
     }
     ```

2. ***什么是转换构造函数***

   - *只有一个实参的构造函数就是转换构造函数*

     ```cpp
     class Fraction {
     public:
       Fraction(int num, int den=1):m_numerator(num), m_denominator(den) { } // 转换构造函数 
     private:
       int m_numerator;
       int m_denominator;
     };
     ```

3. ***没有`explicit`的转换构造函数***

   - *我们先不谈`explicit`是啥，看来瞧瞧下述代码*

     ```cpp
     class Fraction {
     public:
       Fraction(int num, int den=1):m_numerator(num), m_denominator(den) { } // 转换构造函数 
       Fraction operator + (const Fraction &f) {
         return Fraction(...);
       }
     private:
       int m_numerator;
       int m_denominator;
     };
     ```

   - ***那么转换构造函数他的应用场景是啥***

     ```cpp
     Fraction f(3, 5);
     Fraction d2=f+4; 
     ```

     *来看看这一段代码，首先我们实例化了一个"f"，然后执行下一行代码，肯定会先去找 "+" 这个操作符，找到之后就作用于左手边，f 就是 Fraction，他找到了*

     *但是 "+" 的设计理念 左边是Fraction，所以他就会想能不能把4也转换成 Fraction 类型*

     *那这个时候就是转换构造函数的发挥之地了，4 同时 也是4分之1 正好den默认值为1 所以就会自动转换为4分之1*

4. ***有`explicit`的转换构造函数***

   - *我们来看看没有`explicit`的转换构造函数有没有问题，所以来看下述代码*

     ```cpp
     class Fraction {
     public:
       Fraction(int num, int den=1):m_numerator(num), m_denominator(den) { }
       
       operator double() const {
         return (double)(m_numerator/m_denominator);
       }
       
       Fraction operator + (const Fraction &f) {
         return Fraction(...);
       }
     private:
       int m_numerator;
       int m_denominator;
     };
     
     Fraction f(3, 5);
     Fraction d2=f+4; 
     ```

     *我们在之前的代码的基础上，加了一个 `operator double ()`的操作符重载，但是还是一样的调用，现在就会失败，因为编译器发现，double的走法也一样可以，这样就会有两种不同的走法*

     所以`explicit`出现了

   - *在C++中，`explicit`关键字用来修饰类的构造函数，被修饰的构造函数的类，不能发生相应的隐式类型转换，只能以显示的方式进行类型转换，换一种说法就是 告诉编译器 不要隐式转换 既然是构造函数 就真的要用到构造函数的时候在用到*

## ***Effective c++ item***

1. *对于更多的构造函数一些规范及其知识点，请看《[Effective c++ item5~item12](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE5-12-%E6%9E%84%E9%80%A0%E6%9E%90%E6%9E%84%E8%B5%8B%E5%80%BC%E8%BF%90%E7%AE%97/)》*

