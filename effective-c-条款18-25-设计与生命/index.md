# 条款18～25 设计与生命


## ***条款18 让接口容易被正确使用，不易被误用***

### ***对class的参数类型进行抽象***

*我们普通的定义一个接口会有啥问题，如下述代码*

- ```cpp
  class Date{
  public:
      Date(int month, int day, int year);
      ...
  };
  Date d(2, 30, 2021);  // 传参容易出错
  ```

*所以我们可以对class的参数类型进行抽象，如下述代码*

- ```cpp
  struct Day{
      int val;
      explicit Day(int d) : val(d) {}
  };
  ...
  class Date{
  public:
      Date(const Month& m, const Day& d, const Year& y);
      ...
  };
  Date d(Month(3), Day(31), Year(2021));  // 传参可靠
  ```

### ***尽量让自定义type行为与内置type一致***

*这边如果a和b是自定义类型，这边编译器就不会报错，如果a和b是内置类型，那么a*b是临时对象-右值，所以编译器就会报错*

*所以我们这边需要统一，行为与内置type一致，在前面的条款3也说了，直接加const即可*

```cpp
if (a * b = c) 
```

### ***factory 函数返回智能指针***

*了解设计模式的童鞋们就会知道有一个设计模式叫工厂模式，在这里推荐工厂函数返回一个智能指针，因为用裸指针就会增加用户的心智开销，避免忘记delete*

```cpp
Investment* createInvestment(); -> std::shared_ptr<Investment> createInvestment();

shared_ptr<Investment> createInvestment(){
    shared_ptr<Investment> retVal(
        static_cast<Investment*>(0),   // 初始化一个null shared_ptr指针，
        getRidOfInvestment);           // 指定getRidOfInvestment函数为删除器，
    retVal = ...;                      // 令retVal指向正确的对象
    return retVal;
}
```

***总结***

1. *好的接口容易被正确使用，不容易被误用*
2. *“促进正确使用”的办法包括接口的一致性，以及与内置类型的行为兼容*
3. *“阻止误用”的办法包括建立新类型、限制类型上的操作，束缚对象值，以及消除客户的资源管理责任*
4. *shared_ptr 支持定制型删除器。可防范 DLL 问题，可被用来自动解除互斥锁（mutex）等*

## ***条款19 设计class犹如设计type***

*这个条款有12问帮你设计好一个class，当然class由于需求是会多变的，但是这个12问能够起到很好的辅助作用*

1. *自定义 type 的对象应该如何被创建和销毁*
   - *也就是他的构造和析构应该是怎么去写*
2. *对象的初始化和对象的赋值应该有什么样的差别？（不要混淆“初始化”和“赋值”）*
3. *新 type 的对象如果被 pass by value，意味着什么？（copy 构造函数定义）*
4. *什么是新 type 的“合法值”？（构造函数、赋值操作、setter 函数）*
5. *新 type 需要配合某个继承图系吗？（virtual 函数、virtual 析构函数）*
6. *新 type 需要什么样的转换？（条款15：显式转换：explicit 构造，隐式转换：operator Type、->）*
7. *什么样的操作符和函数对此新 type 而言是合理的？*
8. *什么样的标准函数应该驳回？（条款06：明确拒绝copy 构造、copy assignment 操作符等）*
9. *谁该取用新 type 的成员？（访问规则：数据成员的封装，成员函数 API 的开放，是否友元）*
10. *什么是新 type 的“未声明接口”？*
11. *你的新 type 有多么一般化？（class template）*
12. *你真的需要一个新 type 吗？（多个 non-member 函数或 template 是否可以达到目的）*

## ***条款20 以 pass by reference to const 替换 pass by value***

1. *pass-by-reference-to-const 作为函数参数*

   - *效率高（没有构造和析构函数被调用），还能避免slicing（对象切割）问题*

     ```cpp
     class Window{
     public:
         ...
         string name() const;           // 返回窗口名称
         virtual void display() const;  // 显示窗口和内容
     };
     class WindowWithScrollBars : public Window{
     public:
         ...
         virtual void display() const;
     };
     
     void printNameAndDisplay(Window w){  // 不正确！参数可能被切割。value是没有多态的特性的
         cout << w.name();
         w.display();
     }
     WindowWithScrollBars wwsb;
     printNameAndDisplay(wwsb);  // 总是调用Window的display()
     
     
     void printNameAndDisplay(const Windows& w) {  // 正确！引用本质上就是一个漂亮的指针 也具有多态特性
         cout << w.name();
         w.display();
     } 
     ```

2. *pass-by-value 经常适用于内置类型、STL迭代器和函数对象*

## ***条款21 不要错误的返回对象的引用***

1. *不要返回一个临时对象的引用*
2. *不要返回在堆上分配的对象的引用，因为这违背了new和delete成对出现的原则，这样的方式是很不合理的，稍加不注意就会导致内存泄漏问题*
3. *也不要返回一个static对象的引用，因为static可能同时被很多地方需要，这样的话共享就存在问题*
4. ***所以对于这种问题，最好的解决方法就是不返回引用就OK了***

## ***条款22 将成员变量声明为private***

1. *其实声明为private 带来的最大的好处就是 - 成员变量有更精细的访问控制*
2. *考虑一个 public 成员变量变更或消失时对用户代码的影响*
3. *考虑一个 protected 成员变量变更或消失时对 derived class 代码的影响*

## ***条款23 将non member & non friend 替换 member func***

1. *封装使我们能够改变事物而只影响有限客户*
2. *导致较大封装性的是 non-member、non-friend 函数（因为不增加“能够访问 class 内 private 成分”的函数数量）*

```cpp
class WebBrowser{
public:
    ...
    void clearCache();
    void clearHistory();
    void removeCookies();
    ...
};

// non-member“便利函数”
void clearBrowser(WebBrowser& wb){
    wb.clearCache();
    wb.clearHistory();
    wb.removeCookies();
}
```

*将所有便利函数放在多个头文件但隶属于同一个命名空间，意味客户可以轻松扩展这一组便利函数*

```cpp
// 头文件“webbrowser.h”
namespace WebBrowserStuff{
class WebBrowser{...};
    ...                     // 核心机能，几乎所有客户需要的non-member函数
}

// 头文件“webbrowserbookmarks.h”
namespace WebBrowserStuff{
    ...                     // 与书签相关的便利函数
}

// 头文件“webbrowsercookies.h”
namespace WebBrowserStuff{
    ...                     // 与cookie相关的便利函数
}
```

## ***条款24 若所有参数皆需类型转换，请为此采用 non-member 函数***

1. *隐式转换与 member 函数*

   ```cpp
   class Rational{
   private:
       ...
   public:
       Rational(int numerator = 0,     // non-explicit
                int denominator = 1);  // 允许int-to-Rational隐式转换
       int numerator() const;          // 分子访问函数
       int denominator() const;        // 分母访问函数
       ...
       const Rantional operator*(const Rational& rhs) const;
   };
   
   Rational result;
   Rational oneHalf(1, 2);
   
   result = oneHalf * 2;    // 正确，oneHalf.operator*( 2 /* 隐式转换 */);
   result = 2 * oneHalf;    // 错误，2.operator*(oneHalf);
   ```

2. *隐式转换与 non-member 函数*

   ```cpp
   class Rational{
   private:
       ...
   public:
       Rational(int numerator = 0,     // non-explicit
                int denominator = 1);  // 允许int-to-Rational隐式转换
       int numerator() const;          // 分子访问函数
       int denominator() const;        // 分母访问函数
       ...
   };
   const Rantional operator*(const Rational& lhs, const Rational& rhs){
       return Rational(lhs.numerator() * rhs.numerator(),
                       lhs.denominator() * rhs.denominator());
   }
   
   Rational result;
   Rational oneHalf(1, 2);
   
   result = oneHalf * 2;    // 正确，operator*(oneHalf, 2 /* 隐式转换 */);
   result = 2 * oneHalf;    // 正确，operator*(2, oneHalf);
   ```

## ***条款25 考虑写出一个不抛异常的 swap 函数***


