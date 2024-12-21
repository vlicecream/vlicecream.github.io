# 条款5～12 构造/析构/赋值运算


# 条款5～12 构造/析构/赋值运算

## ***条款5-了解c++默认生成的函数***

1. ***前言***
   
   - *这本书其实有点老，该条款有很多东西过时了，所以该条款被 "Effective Modern C++" 条款17 特种成员函数的生成机制 替换*
   
   - *So? 直接去看这个条款吧*

## ***条款6-不想要的特种成员函数，明确禁止编译器自动生成***

1. *前言*
   
   *该条款解决方法 分为两种，一个为"c++11"以后的解决方法，一个为"c++98"*

2. *c++11*
   
   *直接在不想要的特种成员函数 后面 加上 `= delete` 即可*
   
   ```cpp
   class HomeForSale {
   public:
     HomeForSale(const HomeForSale&) = delete;
     HomeForSale& operator=(const HomeForSale&) = delete;
   }
   ```

3. *c++98*
   
   *可以通过私有化 并且 只声明，而不去定义，具体如下述代码*
   
   ```cpp
   class Uncopyable {
   protected:
     Uncopyable() { };
     ~Uncopyable() { };
   private:
     Uncopyable(const Uncopyable&);
     Uncopyable& operator=(const Uncopyable&);
   }
   
   class HomeForSale : private Uncopyable {
     ...
   };
   ```

### ***Summary***

1. *我们在不想要的特种成员函数，可以通过以下方法来禁止编译器自动生成*
   - *c++11 - 在成员函数后面 `= delete`*
   - *c++98 - 放在私有数据里，只声明，不定义*

## ***条款7-为多态基类声明virtual析构函数***

1. *为多态基类声明virtual析构函数*
   
   *如果不为多态基类声明`virtual`析构函数，那么在实例化派生类的时候，则会只调用基类的析构函数，就会造成内存泄漏*
   
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

2. *不是为了多态特性，不要随意声明virtual析构函数*
   
   *如果不需要多态特性，还会析构函数声明了`virtual`，那么析构函数会变大，多了虚指针和虚表*

3. *类继承时小心父类析构函数不具有多态特性*

### ***Summary***

1. *为多态基类声明virtual析构函数*
2. *类继承时小心父类析构函数不具有多态特性*
3. *不是为了多态特性，不要随意声明virtual析构函数*

## ***条款8-析构函数不要抛出异常***

1. *析构函数不要抛出异常*
   
   *析构函数抛出异常就代表这个析构函数并没有执行完，可能会造成内存泄漏*

2. *避免异常从析构函数抛出的方法*
   
   *场景描述：*
   
   ```cpp
   class DBConnection {
   public:
     static DBConnection FactoryCreate(); // 工厂函数
     void close(); // 抛出异常
   };
   
   class DBConn {
   public:
     ~DBConn() 
     {
       db.close(); // 这样的话 其实close方法是可能会抛出异常的
     }
   private:
     DBConnection db;
   }
   ```
   
   *发生异常直接终止程序（程序：你清高）*
   
   ```cpp
   DBConn::~DBConn()
   {
     try { db.Close(); }
     catch (...) {
       // 打印日志
       std::abort(); //终止程序
     }
   }
   ```
   
   *或者就是不终止程序了，直接把错误吞掉，打印好日志*
   
   ```cpp
   DBConn::~DBConn()
   {
     try { db.Close(); }
     catch (...) {
       // 打印日志
     }
   }
   ```
   
   *前两者都无法对"抛出异常"做出什么反应,另一个方法是避免异常函数在析构函数内执行,由客户来调用func函数,为避免客户忘记执行,需设立flag标记客户是否调用,如果客户没有调用,在析构函数内调用该函数*
   
   ```cpp
   class DBConn {
   public:
     void close() {
       db.close();
       closed = true; // 如果成功close 就设一个标识符
     }
     ~DBConn() {
       if (!closed) {
         try { db.close(); }
         catch (...) {
           // make log
         }
       }
     }
   private:
     DBConnection db;
   
   }
   ```

### ***Summary***

1. *析构函数不要抛出异常，因为抛出异常后，接下来的代码无法运行，就等于说析构没有全部执行，会造成内存泄漏*
2. *如果真的抛出异常了，有以下解决方法*
   - *记录log，并直接中止程序（想都不用想，其实不太可能）*
   - *记录log，直接无视错误，继续执行（其实有点不太好）*
   - *增加标志符，将抛出异常的函数交给用户去调用，如果用户没有调用，则在析构中调用，并记录log*

## ***条款9-不要再构造和析构函数中调用virtual函数***

1. *不要再构造和析构函数中调用virtual函数*
   
   *如果在构造函数中调用了virtual函数，在实例化了派生类的时候，构造函数的执行顺序是从内到外，所以他会先调用父类的构造函数，但是父类的构造函数又调用了virtual函数，这时候派生类还没有初始化，所以调用的也只是父类的virtual函数*
   
   *如果在析构函数中调用了virtual函数，在销毁派生类的时候，析构函数的执行顺序是从外到内的，所以他会先销毁子类，再是父类，但是父类的析构函数又调用了virtual函数，这时候派生类已经被干掉了，所以调用的也只是父类的virtual函数*
   
   ```cpp
   class Base{
   public:
     Base() { sayHello(); }
     virtual void sayHello() {
       std::cout << "Hello Base!" << std::endl;
     }
     virtual void sayBye() {
       std::cout << "Bye Base!" << std::endl;
     }
     virtual ~Base() {
       sayBye();
     }
   }
   
   class Derived : public Base {
     Derived() { }
     void sayHello() override {
       std::cout << "Hello Derived" << std::endl;
     }
     void sayBye() override {
       std::cout << "Bye Derived" << std::endl;
     }
   }
   
   Derived derived; // 实例化派生类
   
   // 结果
   Hello Base!
   Bye Base!
   ```

### ***Summary***

1. *不要再构造和析构函数中调用virtual函数*

## ***条款10 - operator=返回自身引用***

*如果不返回自身引用，那么在某些特殊场景 比如下述代码就会出现问题*

```cpp
class Widget {
public:
  void operator=(const Widget& rhs) { } // return void
}

Widget w1(1), w2(2), w3(3);
w2 = w1 // success
w3 = w2 = w1 // 等同于 w3 = (w2 = w1)
```

*`w3 = w2 = w1` 这行先执行了 `w2 = w1` 然后 就会失败，因为传回来的是一个void 没有办法继续 `w3 = `了*

*所以需要修改为返回自身引用，`=`要返回 其他的类似`+=` 也是同理*

```cpp
class Widget {
public:
  Widget& operator=(const Widget& ths) { return *this; }
  Widget& operator=(int rhs) { return *this; }
  Widget& operator+=(const Widget& ths) { return *this; }
}
```

***这么做的目的就是因为要做一个统一的约定，因为`w3 = w2 = w1`确实是可以的，所以我要做成他们一样***

### ***Summary***

1. *要在 `operator = ` 以及类似操作符 要返回自身引用*

## ***条款11-安全实现赋值运算符***

1. *潜在的自我赋值**
   
   *自我赋值一个bug：如果类里面有动态内存分配，那么在赋值的时候，需要先delete掉原来的，再new一个新的，最后赋值。但如果是自我赋值，那么在delete掉原来的内存的同时，需要赋的值也被delete了（因为都是同一块内存）*
   
   *下面是个例子，假设我们有一个Bitmap类，一个Widget类。其中Widget有一个Bitmap的指针*
   
   ```cpp
   class Bitmap{};
   class Widget {
   public:
       Widget& operator=(const Widget& rhs);
   private:
       Bitmap* pb;
   };
   
   // 防止自我赋值的
   Widget& Widget::operator=(const Widget& rhs){
       delete pb;
       pb = new Bitmap(*rhs.pb);
       return *this
   }
   ```
   
   *解决这个问题的方法就是在前面加个判断*
   
   ```cpp
   class Bitmap{};
   class Widget {
   public:
       Widget& operator=(const Widget& rhs);
   private:
       Bitmap* pb;
   };
   
   // 防止自我赋值的
   Widget& Widget::operator=(const Widget& rhs){
       if (this == *rhs) return *this;
       delete pb;
       pb = new Bitmap(*rhs.pb);
       return *this
   }
   ```
   
   *除此之外还有个问题。如果赋值的时候，new一块新的空间失败了，那么pb会指向一块被delete掉的空间。这样的指针是有害的*
   
   ```cpp
   Widget& Widget::operator=(const Widget& rhs) {
       Bitmap *tmp = pb;
       pb = new Bitmap(*rhs.pb);
       delete tmp;
       return *this;
   }
   ```
   
   *首先上面这个版本申请了一个临时变量保存原始的对象。然后new一个Bitmap并赋值。如果这里出错了，还没到delete，其他的所有东西都保持原样。如果没有出错，则再将原始的空间，通过这个临时变量delete。这就解决了上面的问题*
   
   *然后它还取消了自我赋值的检测。但是他依然可以处理自我检测问题，假如两个指针指向同一个对象，它也会先创建一个新的副本，赋值以后再删除原来的版本*

2. *copy and swap技术*
   
   * 这个技术需要保证swap函数是异常安全的
   
   * *swap具体实现《[条款25](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE18-25-%E8%AE%BE%E8%AE%A1%E4%B8%8E%E7%94%9F%E5%91%BD/)》会解释，这里只做了解*
   
   * *异常安全这个概念请去《[条款29](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE26-31-%E5%AE%9E%E7%8E%B0/)》深入理解*
     
     ```cpp
     Widget& Widget::operator=(const Widget& rhs) {
         Widget tmp(rhs);
         swap(tmp);
         return this;
     }
     ```

### ***Summary***

1. *我们在实现赋值运算符的时候 一定要去注意自我赋值*

## ***条款12-复制对象时勿忘其每一个成分***

1. ***引言****
   
   *在深拷贝和浅拷贝的理解中，我们知道了"拷贝构造函数"，并且也了解了他的构成*
   
   *因此，在pass by value的应用场景中，我们可以写出以下的拷贝构造函数*
   
   ```cpp
   #include <iostream>
   #include<string>
   using namespace std;
   
   class A {
   public:
       A(int i) : count(i) {};
       A(const A& r) {                   // 拷贝构造函数
           count = r.count;
       }
       ~A() {};
       void out() {
           cout << "count:" << count << endl;
       }
   private:
       int count;
   };
   
   int main()
   {
       A a(1);
       A a1(a);              // 将a的临时变量传递进去
   
       a1.out();
       return 0;
   }
   ```

2. *不要忘记复制每一个成员*
   
   *在引言的例子里，我们完整将count通过拷贝构造函数复制了过来。如果我们此时有了新需求，需要给类A增加一个成员x，那么在拷贝构造函数中，一定不要忘记拷贝x!*
   
   ```cpp
   #include <iostream>
   using namespace std;
   
   class A {
   public:
       A(int i, int j) : count(i),x(j) {};
       A(const A& r) {
           count = r.count;
           x = r.x;                                          // 不要忘记赋值成员x!
       }
       ~A() {};
       void out() {
           cout << "count:" << count << endl;
           cout << "x:" << x << endl;
       }
   private:
       int count;
       int x;
   };
   
   int main()
   {
       A a(1, 1);
       A a1(a);
   
       a1.out();
       return 0;
   }
   ```
   
   *值得注意的是：如果我们遗漏了第9行代码，编译器是不会报错的，甚至也能正常运行，但是不会得出想要的结果1*

3. *拥有"显示"拷贝构造函数发生继承*
   
   *在上面的A类中， 我们已经针对A写出了显示的拷贝构造函数。但是如果此时发生了继承会怎么样呢？*
   
   ```cpp
   class B: public A {
   public:
       B(int x, int y) : b1(x),b2(y){};
       B(const B& r){
           b1 = r.b1;
           b2 = r.b2;
       };
       ~B() {};
       void out() {
           cout << "b1:" << b1 << endl;
           cout << "b2:" << b2 << endl;
       }
   private:
       int b1, b2;
   };
   ```
   
   *简直跟class A一样有木有*
   
   *于是我们开始编译 `B b(2,2); B b1(b);` ,会出现以下错误：`error C2512: 'CPS' : no appropriate default constructor available`*
   
   *这是为什么呢*
   
   *在类B的拷贝函数中看起来好像赋值了B中的每一个东西，但是它们复制的只是B声明的成员变量b1,b2;B继承A的成员变量附件完全木有得到复制啊*
   
   *在创建对象时，会首先调用A类的构造函数。而在B的初始化列表中，并没有显示的对基类的构造函数进行调用。那么，系统会默认调用A的无参构造函数，但是你的A类并没有定义无参构造函数，所以出错了*
   
   *改法如下*
   
   ```cpp
   class B: public A {
   public:
       B(int x, int y) : b1(x),b2(y), A(x, y) {};    // 调用A的构造函数
       B(const B& r) : A(r.b1, r.b2){                // 调用A的构造函数
           b1 = r.b1;
           b2 = r.b2;
       };
       ~B() {};
       void out() {
           cout << "b1:" << b1 << endl;
           cout << "b2:" << b2 << endl;
       }
   private:
       int b1, b2;
   };
   ```

### ***Summary***

1. *拷贝函数应该确保复制对象内的所有成员变量及所有的基类成分*
2. *不要尝试以某个拷贝函数实现另一个拷贝函数。应该将共同机能放进第三个函数中，并由两个拷贝函数共同调用*

