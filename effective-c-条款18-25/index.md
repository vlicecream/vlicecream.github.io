# 条款18～25 设计与生命


# 条款18～25 设计与生命

## ***条款18 让接口容易被正确使用，不易被误用***

1. *对class的参数类型进行抽象*
   
   *我们普通的定义一个接口会有啥问题，如下述代码*
   
   ```cpp
   class Date{
   public:
       Date(int month, int day, int year);
       ...
   };
   Date d(2, 30, 2021);  // 传参容易出错
   ```
   
   *所以我们可以对class的参数类型进行抽象，如下述代码*
   
   ```cpp
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

2. *尽量让自定义type行为与内置type一致*
   
   *这边如果a和b是自定义类型，这边编译器就不会报错，如果a和b是内置类型，那么a*b是临时对象-右值，所以编译器就会报错*
   
   *所以我们这边需要统一，行为与内置type一致，在前面的条款3也说了，直接加const即可*
   
   ```cpp
   if (a * b = c) 
   ```

3. *factory 函数返回智能指针**
   
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

### ***Summary***

1. *好的接口容易被正确使用，不容易被误用*
2. *“促进正确使用”的办法包括接口的一致性，以及与内置类型的行为兼容*
3. *“阻止误用”的办法包括建立新类型、限制类型上的操作，束缚对象值，以及消除客户的资源管理责任*
4. *shared_ptr 支持定制型删除器。可防范 DLL 问题，可被用来自动解除互斥锁（mutex）等*

## ***条款19 设计class犹如设计type***

### ***Summary***

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
     ```

     void printNameAndDisplay(const Windows& w) {  // 正确！引用本质上就是一个漂亮的指针 也具有多态特性
         cout << w.name();
         w.display();
     } 
     ```

2. *pass-by-value 经常适用于内置类型、STL迭代器和函数对象*

### ***Summary***

1. *pass-by-reference-to-const 作为函数参数，效率高（没有构造和析构函数被调用），还能避免slicing（对象切割）问题*
2. *pass-by-value 经常适用于内置类型、STL迭代器和函数对象*

## ***条款21 不要错误的返回对象的引用***

### ***Summary***

1. *不要返回一个临时对象的引用*
2. *不要返回在堆上分配的对象的引用，因为这违背了new和delete成对出现的原则，这样的方式是很不合理的，稍加不注意就会导致内存泄漏问题*
3. *也不要返回一个static对象的引用，因为static可能同时被很多地方需要，这样的话共享就存在问题*
4. ***所以对于这种问题，最好的解决方法就是不返回引用就OK了***

## ***条款22 将成员变量声明为private***

### ***Summary***

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

### ***Summary***

1. *封装使我们能够改变事物而只影响有限客户*
2. *导致较大封装性的是 non-member、non-friend 函数（因为不增加“能够访问 class 内 private 成分”的函数数量）*

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

### ***Summary***

1. *若所有参数皆需类型转换，请为此采用 non-member 函数*

## ***条款25 考虑写出一个不抛异常的 swap 函数***

*我们标准库里面的`swap`函数是这么写的，无非就是一个中间值过渡，只要T支持copying(通过copy构造函数和assignment操作符完成)，默认的swap会实现代码就会将类型为T的对象进行置换*

```cpp
namespace std {
  template<typename T>
  void swap(T& a, T& b)
  {
    T temp(a);
    a = b;
    b = temp;
  }
}
```

*这种方法对于内置类型没任何问题，内置类型上的赋值绝对不会抛出异常，并且效率很高。但是如果a,b不是内置类型，就会调用类的copy构造函数和assign函数，并且必须是深拷贝。这样如果类的成员较多就会造成交换的效率很低，特别是针对pimpl实现方法，即成员中包含指针（即资源）时。更好的做法就是直接交换指针就可以了，相当于交换了两个int(指针都是4字节的)，这就比拷贝这个指针指向的资源要快得多*

*关于pimpl可以查看本博客《[C++ Pimpl](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-pimpl/)》*

*如何实现呢？我们先用pimpl手法将Widget的数据成员封装到WidgetImpl中*

```cpp
class WidgetImpl {// class for Widget data;
public:// details are unimportant...
private:
    int a, b, c;// possibly lots of data —
    std::vector<double> v;// expensive to copy!...
};

class Widget {// class using the pimpl idiom
public:
    Widget(const Widget& rhs);
    Widget& operator=(const Widget& rhs)// to copy a Widget, copy its
    {        // WidgetImpl object. For
        ...// details on implementing
        *pImpl = *(rhs.pImpl);// operator= in general,
        ...// see Items 10, 11, and 12.
    }
    ...
private:
    WidgetImpl *pImpl;// ptr to object with this 
};// Widget’s data
```

***设计问题***

- *置换两个Widget对象过于复杂，浪费空间和效率(对于置换Widget对象值，我们只需要做的是置换impl指针，但默认的swap要交换Widget类更需要交换WidgetImpl)*
- *可以直接交换指针的地址，改变指针指向的内存*

*我们可以置换其impl指针*

```cpp
namespace std{
    template<>
    void swap<Widget>(Widget& a,Widget& b)//std::swap的全特化版本只能对
           //<Widget>表示这一特例化版本只是针对指针交换而设计
    {
        swap(a.pImpl,b.pImpl);    
    }
}
```

*这个有个问题 pImpl是属于Widget的private成员因此此函数肯定是无法编译通过的，我们可以将其声明为friend函数但其封装性较弱，可以将swap声明为member函数如下*

```cpp
class Widget {
// same as above, except for the
public:// addition of the swap mem func
...
    void swap( Widget& other){
        using std::swap;// the need for this declaration
                    // is explained later in this Item
        swap(pImpl, other.pImpl);// to swap Widgets, swap their
        }
        // pImpl pointers
        ...
};
namespace std {
template<>// revised specialization of
void swap<Widget>(Widget& a,// std::swap
                        Widget& b)
{
    a.swap(b);// to swap Widgets, call their
}// swap member function
}
```

*这一段代码能够通过编译，并且具有STL容器的一致性，以为std::swap也提供了有pulic swap成员函数的和std::swap的特化版本*

*但是对于Widget class templates而非classes 将数据类型加以参数化*

```cpp
template<typename T>
class WidgetImpl { ... };
template<typename T>
class Widget { ... };
namespace std {
template<typename T>
void swap<Widget<T> >(Widget<T>& a,// error! illegal code!
        Widget<T>& b)
{
     a.swap(b); 
}
}

// 企图偏特化一个function template(std::swap)，但C++只能对class templates偏特化
// 在function templates身上时不能偏特化的。因此无法编译
```

*所以我们得偏特化 function template*

```cpp
namespace std {template<typename T>// an overloading of std::swap 
void swap(Widget<T>& a,// (note the lack of “<...>” after
                Widget<T>& b)// “swap”), but see below for
{ 
    a.swap(b); 
}// why this isn’t valid code}
```

*这时候还是有个问题，重载function templates是没问题的，但std是一个特殊的命名空间*

- *可以全特化std内的templates*

- *不可以添加新的templates(class或function)到std里面*
  
  ***所以真正高效正确的做法就是 non member swap & member swap相结合***

```cpp
namespace WidgetStuff {
...// templatized WidgetImpl, etc.
template<typename T>// as before, including the swapclass 
Widget { ... };// member function
...
template<typename T>// non-member swap function;
void swap(Widget<T>& a,// not part of the std namespace
                    Widget<T>& b)
{
        a.swap(b);
}
}
```

*swap实现效率不足的解决(class或template运用了pimpl手法)*

1. *提供一个public的swap函数，让它高效地置换你的类型的两个对象值，而其不能抛出异常*
2. *在你的class或template所在的命名空间提供一个non-member swap，并令他调用上述swap成员函数*
3. *如果编写一个**class(而非class template)**，为你的class特化一个std::swap，并令他调用你的swap的成员函数*
4. *必须使用using std::swap,以便其能够在函数类曝光可见，然后报价namspace修饰符*

### ***Summary***

1. *当std::swap对你的类型无效时，提供一个swap成员函数。确保交换不会抛出异常*
2. *如果你提供了一个成员交换，也要提供一个调用成员的非成员交换。对于类(不是模板)，也要专门化std::swap*
3. *当调用swap时，对std::swap使用using声明，然后不带命名空间限定的调用swap*
4. *完全专门化用户定义类型的std模板是可以的，但永远不要尝试向std添加全新的东西*

