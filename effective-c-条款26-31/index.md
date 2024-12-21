# 条款26～31 实现


# 条款26～31 实现

## ***条款26 尽可能延后变量定义得时间***

1. *因为变量（对类而言）的定义，需要承担一次构造函数的时间，在函数结束后还可能承担一次析构函数的时间，假如该变量未被使用，那么构造函数和析构函数的时间就白白浪费了，尤其是在可能发生异常的函数中，假如你过早的定义变量，然后在你使用这个变量之前抛出了异常，那么这个变量的构造函数就没有意义而且降低效率。所以应该尽可能延后变量定义得时间，只有真正使用这个变量的时候才定义它*

2. *[条款4](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE1-4-%E4%B9%A0%E6%83%AFc-/#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E6%88%90%E5%91%98%E5%88%9D%E5%A7%8B%E5%8C%96%E5%88%97%E8%A1%A8%E4%BC%98%E4%BA%8E%E5%87%BD%E6%95%B0%E4%BD%93%E8%B5%8B%E5%80%BC)讲过，copy construction的效率 > default construction +assign function，所以最好的做法是直接调用copy construction函数对变量直接进行初始化，而不是先定义，再赋值*
   
   ```cpp
   std::string encryptPassword(const std::string& password) {
     ...
     string encrypted;
     encrypted = password;
     ...
   }
   
   std::string encryptPassword(const std::string& password) {
     ...
     string encrypted(password)
     ...
   }
   ```

3. *对于有循环的情况，假设一个n次的循环，如代码所示*
   
   - *方法A-定义于循环之外*
     
     *此方法的代价 - 1次构造，1次析构，n次赋值*
     
     ```cpp
     Widget w;
     for (int i = 0; i < n; ++i) {
       w = <取决于i的某个值>;
       ...
     }
     ```
   
   - *方法B-定义于循环内*
     
     *此方法的代价 - n次构造，n次析构*
     
     ```cpp
     for (int i = 0; i < n; ++i) {
       Widget w(<取决于i的某个值>);
       ...
     }
     ```
   
   - *如果n较大，那么应该选择方法A / 如果n较小，可以选择方法B*

### ***Summary***

1. *尽可能延后变量定义得时间，只有真正使用这个变量的时候才定义它*
2. *直接调用copy construction函数对变量直接进行初始化，而不是先定义，再赋值*
3. *对于有循环的情况，如果n较大，那么应该选择方法A / 如果n较小，可以选择方法B*

## ***条款27 正确使用类型转换***

1. *`const_cast`：4个类型转换中唯一一个可以对`const`进行转换的类型转换符*

2. *`static_cast`：算是用的最多的一个类型转换符，明确指出类型转换，一般建议都将隐式转换都替换为显示转换，因为没有动态 类型检查，`static_cast`上行转换也就是 派生类->基类 安全，但是下行转换不安全，所以主要执行非多态的转换操作，其实基本上除了常量和非多态也就都用`static_cast`了*
   
   *既然是最常用的，那我们来看看static_cast*
   
   - *`static_cast`使用例子*
     
     ```cpp
     class Widget {
     public:
       explicit Widget(int size);
     ...
     };
     
     void doSomeWork(const Widget& w);
     doSomeWork(Widget(15)); // create Widget from int with function-style cast
     doSomeWork(static_cast<Widget>(15)); // create Widget from int with c++-style cast
     
     // 关于 function-style 大家可以看这个网址
     // url: https://en.cppreference.com/w/cpp/language/explicit_cast
     ```
   
   - *`static_cast`转型分析*
     
     ```cpp
     #include <stdio.h>
     class Base {
     public:
       int a;
     };
     
     class Dervied : public Base {
     public:
       double c{};
       virtual void bar()
     };
     
     int main() {
       Dervied d;
       Base* base = static_cast<Base*>(&d);
     
     }
     ```
   
   - *`static_cast<xxx>(zzz)`其实是一个临时对象*
     
     *如下述代码，如果`Window::onResize()`中有修改成员变量的操作，那么在`SpecialWindow`中是修改不了的，因为转换后只是一个临时对象*
     
     ```cpp
     class Window {
     public:
       virtual void onResize() { ... };
     };
     
     class SpecialWindow : public Window {
     public:
       virtual void onResize() {
         static_cast<Window>(*this).onResize();
       }
     }
     
     // 正确写法
     class SpecialWindow : public Window {
     public:
       virtual void onResize() {
         Window::onResize();
       }
     }
     ```

3. *`dynamic_cast`：专⻔用于派生类之间的转换，type-id 必须是类指针，类引用或 void*，对于下行转换是安全的，当类型不一致时，转换过来的是空指针，而static_cast，当类型不一致时，转换过来的是错误意义的指针，可能造成非法访问等问题*
   
   *他的代价也比较高，因为其他的都是在编译时，这个是在运行时*
   
   - *`dynamic_cast`的开销有点大，所以能避免就避免*
     
     - *此解决方法1 很鸡肋 因为原本想用基类指针，这样方便扩展类*
       
       ![使用dynamic_cast的解决方法1](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303101818186.png)
     
     - *针对上述问题，我们可以这么改*
       
       ![解决方法2](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303101820910.png)

4. *`reinterpret_cast`：不到万不得已，不要使用这个转换符，高危操作*
   
   - *使用特点: 从底层 对数据进行重新解释，依赖具体的平台，可移植性差*
   - *可以将整形转换为指针，也可以把指针转换为数组; 可以在指针和引用之间进行肆无忌惮的转换*

### ***Summary***

1. *最好使用C++4个新式的类型转换函数，因为这很容易辨识，代码可读性提高*
2. *尽量避免使用`dynamic_cast`，因为这种转换效率很低，一般用虚函数的方式来避免转型*

## ***条款28 避免返回一个指针、引用或者迭代器指向类内的成员***

1. *如果返回了成员的引用或者指针，就可以通过这个引用或者指针修改雷内的private成员，这样是不合理的（这样的话成员就相当于public的了），这一点可以通过给函数的返回类型加const修饰符来防止内部成员变量被修改*
2. *但是还有一种情况是，如果获得的类内的一个成员的引用或指针，但是在使用之前，对象被释放了，那么这个引用或指针就变成了野指针了，必然会导致core dump错误。所以应该避免返回类内成员的指针或引用*

## ***条款29 努力写一个异常安全的代码***

1. *这个条款的核心思路就是 发生异常时的处理主要分一下几类：资源不泄漏、数据不丢失、不抛出异常。反正就是考虑程序的各种可能的情况，如果异常了要尽可能保证你的程序某些功能或数据不丢失*
   
   - *RAII，不清楚RAII，可以去看《[RAII是什么](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-raii%E6%98%AF%E4%BB%80%E4%B9%88/)》*
     
     ```cpp
     class PrettyMenu {
     public:
       void changeBackground(std::istream& imgSrc);
     private:
       Mutex mutex;
       Image *bgImage;
       int imageChanges;
     };
     
     void PrettyMenu::changeBackground(std::istream& imgSrc) {
       lock(&mutex);
       delete bgImage;
       ++imageChanges;
       bgImage = new Image(imgSrc); // 可能出现异常
       unlock(&mutex)
     }
     ```
     
     *如果在` bgImage = new Image(imgSrc);`出现异常 那么你就会发现，数据已经出现修改并且锁也没有释放，我们称这种代码就是**异常不安全代码***
     
     *我们可以利用 **RAII** 这么修改*
     
     ```cpp
     void PrettyMenu::changeBackground(std::istream& imgSrc) {
       lock_guard(&mutex); // lock_guard就是c++11之后的一个RAII对象
       delete bgImage;
       ++imageChanges;
       bgImage = new Image(imgSrc); // 可能出现异常
     }
     ```
     
     *我们利用RAII能够成功的把锁给释放了，但是数据破坏还没有解决*
   
   - *智能指针*
     
     ```cpp
     class PrettyMenu {
       ...
       std::shared_ptr<Image> bgImage;
       ...
     }
     
     void PrettyMenu::changeBackground(std::istream& imgSrc) {
       lock_guard(&mutex); // lock_guard就是c++11之后的一个RAII对象
       bgImage.reset(new Image(imgSrc));
       ++imageChanges;
     }
     ```
     
     *这样这段代码就是一个基本异常安全的代码*

2. *异常安全也是分等级的，异常安全代码只需保证下面其一就可以说是异常安全代码*
   
   - *basic guarantee 基本异常安全，就如上述例子一样，不会数据破坏，资源泄漏*
   - *strong guarantee 强力异常安全，如果函数失败，此时状态还是执行函数之前的状态*
   - *nothrow guarantee 不会抛出异常的*

3. *怎么能做到强力异常安全*
   
   - *copy and swap 该策略是《[Pimpl](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-pimpl/)》的一种使用实现*
     
     ```cpp
     struct PMImpl {
       std::shared_ptr<Image> bgImage;
       int imageChanges;
     }
     
     class PrettyMenu {
       ...
     private:
       Mutex mutex;
       std::shared_ptr<PMImpl> pImpl;
     }
     
     void PrettyMenu::changeBackground(std::istream& imgSrc) {
       using std::swap;
       Lock ml(&mutex);
       std::shared_ptr<PMImpl>;
       pNew->bgImage.reset(new Image(imgSrc));
       ++pNew->imageChanges;
       swap(pImpl, pNew);
     }
     ```

4. *实现强异常安全还是比较有困难的，所以我们要保证一个基本的异常安全，但是如果能实现强力异常安全，那也还要去实现的*
   
   - *看下述代码，如果`f1()` `f2()`只是基本异常安全，然而我们想要把`someFunc()`实现为强异常安全，那代价将是非常高的*
     
     *因为，我们要捕捉`f1()` 的所有异常，然后在`f1()`执行之前将函数目前状态都保存下来，如果异常了就要恢复，所以代价会很高*
     
     *如果`f1()` `f2()`都是强力异常安全，那么行不行呢，也是不行的，因为`f1`执行成功，但是`f2`异常了，这时候`f1`该做的操作也都做了*
     
     ```cpp
     void someFunc() {
       ... // make copy of local state
       f1();
       f2();
       ... // swap modified state into place
     }
     ```

### ***Summary***

1. *发生异常时的处理主要分一下几类：资源不泄漏、数据不丢失、不抛出异常。反正就是考虑程序的各种可能的情况，如果异常了要尽可能保证你的程序某些功能或数据不丢失*
2. *实现异常安全可以依靠下述办法，但不仅依靠下述办法*
   - *RAII*
   - *智能指针*
   - *PImpl - 这个是实现强力异常安全的思路*
3. *我们要做到基本异常安全，强力异常安全就佛系，但是能做到也最好*

## ***条款30 理解inline function***

1. *inline function 会在符号表中以"weak"中存在*
   
   - *下述代码在链接的时候会报错，你这个全局其实有两个`foo()`函数*
     
     ![non-inline](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303102245825.png)
     
     *我们来看看上述代码的符号表，在符号表中其实`foo(int)`符号绑定类型是GLOBAL*
     
     ![non-inline 符号表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303102246364.png)
   
   - *我们用inline function来试试*
     
     ![inline function符号表](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303102249798.png)

2. *c++17 之后静态成员直接类内inline就可以省去类外初始化的麻烦*
   
   ```cpp
   inline static uint8_t selectone = 0;
   ```

3. *c++11之后inline可以修饰命名空间*
   
   - 下述代码 在`libfoo_2022`命名空间中会有一个模板`foo`，然后再`libfoo`命名空间中想来个模板特化，这样是会报错的
     
     ```cpp
     namespace libfoo {
       namespace libfoo_2022 {
         template <typename T>
         T& foo(T&);
       }
       using namespace libfoo_2022;
     }
     
     namespace libfoo {
       template <>
       float& foo<float>(float&);
     }
     ```
   
   - *可以在namespace中加入inline*
     
     ```cpp
     namespace libfoo {
       inline namespace libfoo_2022 {
         void foo1(Bar1);
         class Bar2 {};
         template <typename T>
         T& foo(T&);
       }
       using namespace libfoo_2022;
     }
     
     namespace libfoo {
       template <>
       float& foo<float>(float&);
     }
     ```

4. *隐式 inline*
   
   - *下述场景是一个隐式inline*
     
     ```cpp
     class Person {
     public:
       ...
       int age() const { return theAge; } // 隐式inline
       ...
     private:
       int theAge;
     };
     ```
   
   - *inline也可以修饰模板对吧，在符号表中，其实模板也是weak符号类型*

### ***Summary***

1. *inline function 会在符号表中以"weak"中存在*
2. *c++11之后inline可以修饰命名空间*
3. *c++17 之后静态成员直接类内inline就可以省去类外初始化的麻烦*
4. *隐式 inline，对于模板来说都是inline*

## ***条款31 最小化文件依赖***

*我们在项目里只修改了一个私有成员，然后编译就发现全部重新编译了，我们就需要构建一个"编译防火墙"，编译防火墙就是最小化文件依赖*

*我们来看看c++的一个特点，在下述代码中，如果我们修改了`Date.h`或者`Address.h`，那么只要包含了`Person.h`的文件就会重新编译*

- ```cpp
  #include <string>
  #include "Date.h"
  #include "Address.h"
  
  class Person {
  public:
    Person(const std::string& name, const Date& birthday, const Address& addr);
    std::string name const;
    Date birthDate() const;
    Address address() const;
  private:
    std::string theName;
    Date theBirthDate;
    Address theAddress;
  }
  ```

*然后我们继续基于上述代码来看`main()`*

- ```cpp
  int main() {
    int x;
    Person p(params);
    ...
  }
  ```

*在执行到`Person p(params);`这一行 编译器就必须看清楚`Person`是啥，特别是私有成员变量里的`Date` `Address`*

*但是我们如果定义成一个指针，编译器就不会去看清楚*

- ```cpp
  int main() {
    int x;
    Person* p;
    ...
  }
  ```
1. *还是《[Pimpl](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-pimpl/)》的设计，我们可以通过"PImpl"来最小化文件依赖*
   
   - *我们采用了PImpl设计，并且前置声明，但是有几个点一定要注意*
     - *如下述代码，`Person::~Persion() = default;`一定要写出来，如果不写，编译器默认会加，于是所有包含该头文件的编译单元都会inline编译这几个函数，然而又找不到数据类的实现，于是出现编译错误*
     - *Person构造函数如下述代码用智能指针写*
     - ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303102346753.png)

2. *除了"PImpl"，我们还可以用 抽象类接口 最小化文件依赖*
   
   - *类中全部都是pure virtual函数，这样的类在使用的时候只能是以指针的形式出现，这样就同样达到了减少编译依赖的效果*
     
     ![抽象类接口](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303110002425.png)
   
   - *但是希望的能明白，这两种方式都可以最小化依赖，没有什么好与不好。因为PImpl他多出来指针，而且每次访问都是间接访问。虚函数也是靠虚表和虚指针，所以都会带来一定的开销，写的时候任意选一个熟悉的即可，本人是习惯抽象类接口*

### ***Summary***

1. *我们可以通过"PImpl"来最小化文件依赖*
   - *要注意析构函数必须显示的写出来*
2. *我们还可以用 抽象类接口 最小化文件依赖*
3. *PImpl他多出来指针，而且每次访问都是间接访问。虚函数也是靠虚表和虚指针，所以都会带来一定的开销*

