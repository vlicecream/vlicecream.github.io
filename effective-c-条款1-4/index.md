# 条款1～4 让自己习惯c++


# 条款1～4 让自己习惯c++

## ***条款1-视c++为一个语言联邦***

1. *视c++为一个语言联邦*
   
   - *C*
   
   - *Object-Oriented C++*
   
   - *Template C++*
   
   - *The STL*

### ***Summary***

1. *我们想要学习c++，一定就得学这4个，分别是c，c++的面向对象等特性，c++模板编程/泛型编程，STL*

## ***条款2-尽量不使用#define***

1. *`#define`定义常量*
   
   - *`#define`定义常量的缺点*
     
     - *`#define ASPECT_RATIO 1.653`，`#define`是不会添加到符号表的，所以当跟ASPECT_RATIO有关报错的时候，不会提示ASPECT_RATIO出了问题，不利于报错排查*
   
   - *替换手段*
     
     - *可以拿`const double AspectRatio = 1.653;`来进行替换`#define`*
     
     - *在类中也可以拿static来定义"常量"*
       
       ```cpp
       class GamePlayer {
       private:
           static const int NumTurns = 5;
         int scores[NumTurns];
       }
       ```
       
       *但是要注意 在编译的时候这个是通过的，但是如果你在后面对他有取地址之类的操作，链接就会报错，因为这里只是声明，并没有给他分配内存空间，所以还需要在cpp处理一下即可*
       
       ```cpp
       const int GamePlayer::NumTurns;
       ```

2. *`#define`定义宏*
   
   1. *`#define`定义宏的缺点*
      
      - *其实在inline专题有提到，`#define`定义宏，太麻烦了，只因他是直接替换的效果，最经典就是计算平方问题*
        
        ```cpp
        #define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))
        ```
   
   2. *替换手段*
      
      - *可以拿`inline`函数去进行替换*
        
        ```cpp
        template<typename T>
        inline void callWithMax(const T& a, const T& b)
        {
          f(a > b ? a : b);
        }
        ```

### ***Summary***

1. *`#define`由于各个的因素，比如安全性，使用性，不太咋滴，所以尽量不使用`#define`*
   - *常量可以由`const`或者`static`替换*
   - *复杂的宏计算表达式就用`inline`替换*

## ***条款3-尽量使用const***

1. *修饰指针*
   
   - *修饰指针一定要区分好，const pointer and const data*
     
     ```cpp
     char greeting[] = "Hello";
     char* p = greeting; // non-const pointer, non-const data
     const char* p = greeting; // non-const pointer, const data
     char* const p = greeting; // const pointer, non-const data
     const char* const p = greeting; // const pointer, const data
     
     void f1(const Widget *pw); // f1 takes a pointer to a constant Widget object
     void f2(Widget const *pw); // so does f2
     ```

2. *修饰 STL-iterator*
   
   - *修饰STL-iterator所带来的好处就是可以控制 iterator指向的值或者iterator本身是否被修改，同时这也是红黑树，map，set控制修改iterator指向的值的秘诀*
     
     ```cpp
     std::vector<int> vec;
     // ...
     const auto iter = vec.begin(); // iter acts like a T* const
     *iter = 10; // OK, changes what iter pointer to
     ++iter; // error! iter is const
     
     auto cIter = vec.cbegin(); // cIter acts like a const T*
     *cIter = 10; // error! cIter is const
     ++cIter; // fine, changes cIter
     ```

3. *将某些东西声明为const可以帮助编译器侦测出错误用法*
   
   - *在函数返回值修饰的好处就是可以保证一些低级的错误，如下述代码*
     
     ```cpp
     class Rational { ... }
     const Rational operator * (const Rational& lhs, const Rational& rhs);
     
     Ration a, b, c;
     // ...
     (a * b) = c; // 这是没有错的，但是我们这么写很少 几乎没有
     ```
     
     *我们一般会把这个`(a * b) = c;` 加个 `if`判断，变成`if (a * b == c) {...}`，但是如果我们手抖，写成了`if (a * b = c)` 这个就是赋值了，但是编译器不会报错，但是我们在返回参数加了一个 const，就能够报错*

      - *将`#define`定义常量 修改为`const`也是可以帮助编译器侦测出错误类型*

4. *bitwise constness && logical constness*
   
   *编译器强制实行bitwise constness(又称physical constness,物理上的常量性,即成员函数不更改对象的任何一个bit时才可以说是const),例如*
   
   ```cpp
   class TextBlock{
   public:
       ...
       char& operator [](std::size_t position) const{
           return pText[position];
       }
   private:
       char* pText;
   }
   ```
   
   *编译器认定它是bitwise constness的,但是它却允许以下代码的存在*
   
   ```cpp
   const TextBlock cctb("Hello");
   char* pc=&cctb[0];
   *pc='J';
   ```
   
   *这是由于只有pText是cctb的一部分,其指向的内存并不属于cctb*
   
   ***程序员编写程序时应该使用conceptual constness(概念上的常量性或logical constness,逻辑上的常量性,即一个const成员函数可以处理它所修改的对象的某些bits,但只有在客户端侦测不出的情况下才得如此)***
   
   *例如对于某些特殊类,其中的某些成员的值注定是要改变的,因此可以用mutable关键字修饰,从而实现即使对象被设定为const,其特定成员的值仍然可以改变的效果.此时该类符合conceptual constness而不符合bitwise constness.*
   
   *下述代码即就是上一句话的代码解释，在客户端的角度来说，他这个值是不修改的*
   
   ```cpp
   class CTextBlock {
   public:
     std::size_t length() const;
   private:
     char* pText;
     mutable std::size_t textLength;
     mutable bool lengthThisVaild;
   };
   
   std::size_t CTextBlock::length() const
   {
     if (!lengthThisVaild)
     {
       textLength = std::strlen(pText);
       lengthThisVaild = true;
     }
     return textLength;
   }
   ```

5. *函数重载*
   
   *如果参数是引用,可以基于参数是否为const实现函数重载(也可以基于指针是否为const实现函数重载),特殊的,对于成员函数,因为它存在一个隐含的this指针参数,因而可以基于函数是否为const实现重载*
   
   ```cpp
   class TextBlock {
   public:
     const char& operator[](std::size_t position) const { return text[position]; }
     char& operator[](std::size_t position) { return text[position]; }
   private:
     std::string text;
   }
   
   TextBlock tb("hello");
   std:cout << tb[0]; // calls non-const TextBlock::operator[]
   const TextBlock ctb("hello");
   std:cout << ctb[0]; // calls const TextBlock::operator[]
   ```

6. *避免const和非const成员函数中的重复*
   
   ```cpp
   class TextBlock {
   public:
     const char& operator[](std::size_t position) const {
       ... // 检查
       ... // 打印日志
       return text[position];
     }
   
     char& operator[](std::size_t position) {
       ... // 检查
       ... // 打印日志
       return text[position];
     }
   }
   ```
   
   *看上述代码 很明显 他们有重复代码，所以我们要抽出来，因为这样更好维护，代码如下*
   
   ```cpp
   class TextBlock{
   public:
       ...
       const char& operator[](std::size_t position) const{
         ...
         return text[position];
       }
       char operator[](std::size_t position){
           return const_cast<char&>(static_cast<const TextBlock&>(*this)[position]);
       }
       ...
   };
   ```
   
   *可以看出,经过了两次类型转换*
   
   *第一次通过`static_cast`将`*this`转为`const TextBlock&`，以确保调用的是`operator[]`的const版本，否则会调用非const版本导致递归调用造成栈溢出*
   
   *第二次通过const_cast去掉const版本的opsrator[]返回的const char&的const特性以与函数的返回类型相匹配*

7. *对于const_cast的行为之前存在一些误解,对于以下代码*
   
   ```cpp
   #include<iostream>
   using std::cout;
   using std::endl;
   int main(){
       const int a = 5;
       int& rta = const_cast < int&>(a) ;
       rta = 6;
       cout << "a: " << a << "    rtr: " << rta << endl;
       cout << "&a: " << &a << "     &rta: " << &rta;
       system("pause");
       return 0;
   }
   ```
   
   *输出结果如下*
   
   ![img](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303032116653.png)
   
   *可见虽然const_cast表面上改变了变量的const性质,但a的值实际上还是没有改变(编译器仍然背着我们干了不少事),所以const_cast的实际用途并不是改变const对象的值,而是"暂时"去除对象的const属性使其可以作为参数传入非const函数,企图通过const_cast改变const对象的值可能会导致未预料的结果.因此个人认为5中的第二段代码(出自Effective C++ “条款3  尽可能用const”)存在一些错误,如有错误欢迎批评指正！*

### ***Summary***

1. *const可以修饰指针，要注意区分修饰指针*
   - *`const int* i = 1;` non-const-pointer / const-data*
   - *`int* const i = 1;` const pointer / non-const-data*
   - *`const int* const i = 1;` const pointer / const-data*
2. *const可以用来修饰STL中的iterator*
   - *一定要知道红黑树下的map，set的key不可修改就是因为 const iterator*
3. *const安全性比某些东西更高*
   - *没错 某些东西说的就是`#define`*
4. *bitwise constness && logical constness*
   - *关于这个你只需要记住程序员编写程序时应该使用logical constness（概念上的常量性，逻辑上的常量性）*
   - *即一个const成员函数可以处理它所修改的对象的某些bits,但只有在客户端侦测不出的情况下才得如此*
   - *说简单点，就是在客户的角度上看，不需要修改其任何值，我们就必须使用const进行标注，但是在内部其实是修改了值的*
5. *如果参数是引用,可以基于参数是否为const实现函数重载(也可以基于指针是否为const实现函数重载),特殊的,对于成员函数,因为它存在一个隐含的this指针参数,因而可以基于函数是否为const实现重载*
6. *实现const重载时，我们可以利用`static_cast` `const_cast`来去除冗余代码*

## ***条款4-对象使用前应该被初始化***

1. *class的成员变量总是以其声明的次序被初始化*
   
   - *在下述代码中，我们虽然在构造函数的初始化列表中先初始化了`b_`，再是`a_`，但是实际上是以声明的次序被初始化，也就是先初始化 `a_`，在初始化`b_`*
     
     ```cpp
     class C {
     public:
       C(std::string aName, int bValue) : b_(bValue), a_(aName, b_.v_) { }
     private:
       A a_; // 不展示A class了
       B b_; // 不展示B class了
     }
     ```
- *类类型的构造函数对于成员按:**基类成员→派生类类类型成员(按声明顺序)→派生类内置类型成员(按声明顺序)的顺序进行初始化**.*
2. *构造函数成员初始化列表优于函数体赋值*
   
   - *Foo函数如下*
     
     ![Foo](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041320336.png)
- *随后我们来看看 不用构造函数的初始化列是什么情况*
  
  ```cpp
  class Test {
  public:
    explicit Test(const Foo& foo) {
      foo_ = foo;
    }
  private:
    Foo foo_;
  }
  
  int main() {
    Foo foo(1, "foo1");
    Test test(foo);
    return 0;
  }
  ```
  
  *我们来看看不用构造函数的初始化列 到底做了什么*
  
  ![不用构造函数的初始化列](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041323781.png)

- *最后我们再来看看用了构造函数的初始化列，形成一个对比*
  
  ```cpp
  class Test {
  public:
    explicit Test(const Foo& foo) : foo_(foo) { }
  private:
    Foo foo_;
  }
  ```
  
  ![使用了构造函数的初始化列](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041325435.png)

- *我们也是很明显的发现 使用初始化列 明显性能要高于 不使用初始化列，不使用初始化列要先两次构造函数了，然后一步调用拷贝赋值，而使用初始化列只做了一个构造函数和一次拷贝构造函数*
3. *非局部变量的初始化顺序替换为函数运用结合*
   
   - *既然被替换了，那么肯定被替换者有什么问题，那我们来好好瞧瞧*
     
     - *接下来场景为多文件场景，有两个文件，分别是 "init_before_user.cpp", "init_before_user1.cpp"*
     
     - ```cpp
       // init_before_user.cpp
       #include "foo.h"
       Foo globalFoo(123, "globalFoo")
       
       int main() {
         printf("===main===\n");
       }
       ```
       
       ```cpp
       // init_before_user1.cpp
       #include "foo.h"
       extern Foo globalFoo;
       class Bar {
       public:
         explicit Bar(Foo &foo) {
           printf("Bar(const Foo&)foo.a=%d\n", foo.a);
         }
       };
       
       Bar bar(globalFoo);
       ```
       
       *如果我们使用cmake*
       
       ```cpp
       add_executable(init_before_use
                     init_before_use.cpp
                     init_befor_user1.cpp
                     )
       ```
       
       ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041407896.png)
       
       *就会发现Bar的foo.a的值是不对的，这是因为初始化文件的顺序是会影响初始化的顺序的*
       
       ```cpp
       add_executable(init_before_use
                      init_befor_user1.cpp
                     init_before_use.cpp
                     )
       ```
       
       *这样就正常了，但是这样我们肯定是不愿意看到的，坑非常大*
- ***通过函数运用去解决***
  
  - ```cpp
    // init_before_user.cpp
    #include "foo.h"
    Foo& globalFoo() {
      static Foo globalFoo(123, "globalFoo");
      return globalFoo;
    }
    
    int main() {
      printf("===main===\n");
    }
    ```
    
    ```cpp
    // init_before_user1.cpp
    #include "foo.h"
    extern Foo& globalFoo();
    class Bar {
    public:
      explicit Bar(Foo& foo) {
        printf("Bar(const Foo&)foo.a=%d\n", foo.a);
      }
    };
    
    Bar bar(globalFoo);
    ```
    
    *这样解决就不存在之前说的初始化的问题*

### ***Summery***

1. *类的成员变量的初始化顺序不是取决于初始化列表的顺序，而是声明的顺序*
2. *我们要尽量使用构造函数的初始化列表*
   - *因为这里才是真正的初始化，在函数体中那叫赋值*
   - *对于类类型,降低了程序效率，对于内置类型在初始化列表还是在函数体内初始化对于效率没有影响*
   - *在某些特殊情况(例如const变量和引用)必须在初始化列表进行初始化*
3. *非局部变量，比如全局变量，他会存在cmake的文件顺序来决定初始化顺序*
   - *所以使用函数来解决此问题*

