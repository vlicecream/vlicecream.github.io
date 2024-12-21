# 条款13～17 资源管理


# 条款13～17 资源管理

## ***知识点 RAII对象***

1. *本大章节-资源管理，需要弄懂什么是RAII，在本博客《[什么是RAII](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-raii%E6%98%AF%E4%BB%80%E4%B9%88/)》中有详解*

## ***条款13 以对象管理资源***

1. *前言*
   
   - *其实在本文中的`auto_ptr`已经被弃用了，建议去看《[Effective Modern c++ Item18 ~ Item22](https://vlicecream.github.io/effective-modern-c-%E6%9D%A1%E6%AC%BE18-22-%E6%99%BA%E8%83%BD%E6%8C%87%E9%92%88/)》*
   
   - *但是在这里还是说一下该条款的一些思路点*

2. *思路点*
   
   - *在构造中获得资源并在析构函数中释放资源*
   
   - *两个常用的自动管理资源的类是shared_ptr和auto_ptr，其中auto_ptr的复制动作，会导致复制对象变为null，容易造成意外的错误，一般推荐使用shared_ptr，其使用引用计数的原理实现对象共享的目的，并且在计数为0时自动释放对象*

### ***Summary***

1. *已过时，建议去看《[Effective Modern c++ Item18 ~ Item22](https://vlicecream.github.io/effective-modern-c-%E6%9D%A1%E6%AC%BE18-22-%E6%99%BA%E8%83%BD%E6%8C%87%E9%92%88/)》*
2. *思路点其实就是RAII原则*

## ***条款14 在资源管理类中小心copying行为***

1. *引言*
   
   - *上节是对资源的管理说明。有时候我们不能依赖于shared_ptr或者auto_ptr,所以我们须要自己建立一个资源管理类来管理自己的资源*
- *假设我们使用`C API`函数处理`Mutex`互斥器对象：*
  
  ```cpp
  void lock(Mutex* pm);   //锁定pm所指的互斥器
  void unlock(Mutex* pm); //解除锁定
  ```
  
  *为了忘记释放锁，我们想要建立一个class来进行资源管理，这里class应该满足`RAII`守则，即"资源在构造期间获得，在析构期间释放"*
  
  ```cpp
  class Lock{
  public:
      explicit Lock(Mutex* pm):mutexPtr(pm){
          lock(mutexPtr);//获得资源
      }
      ~Lock(){
          unlock(mutexPtr); // 释放资源
      }
  private:
      Mutex* mutexPtr;
  };
  ```
2. *copying行为*
   
   - *上述例子写的这很不错，但是如果此时`Lock`对象被复制，会发生什么？*
     
     ```cpp
     Lock ml1(&m); // 锁定m
     Lock ml2(ml1); // 将ml1复制到ml2身上
     ```
     
     *这样做绝对不行，我们不能确定什么时候m2和m1会被析构，一旦被析构就会导致mutex解锁，mutex一旦解锁就会被别的进程所调用，程序将出现巨大的混乱*
     
     *其实，这不是说一个特定例子，一般的情况是：当一个`RAII`对象被复制时，应该如何选择，有以下两种做法*

3. *禁止复制*
   
   - *如条款06所言：将`copying`操作声明为`private`，或者利用c++11新特性 `= delete`*

4. *引用计数*
   
   - *对底层资源使用”reference-count“*
- *有时候希望保有资源，直到它的最后一个使用者（某对象）被销毁。对`Lock`打算使用`reference counting`它可以改变`mutexPtr`类型，将`Mutex*`改为`tr1::shared_ptr<Mutex>`，当然这不是我们想要的，我们只是想释放锁，而不是删除锁*

- *幸运的是`tr1::shared_ptr`运行指定所谓的删除器，那是一个函数或对象，引用次数为0才被调用。删除器对`tr1::shared_ptr`构造函数而言是可有可无的第二参数，所以代码看起来像这样*
  
  ```cpp
  class Lock {
  public:
    explicit Lock(mutex* pm) : mutexPtr(pm, unlock)
    {
      lock(mutexPtr.get());
    }
  private:
    std::trl::shared_ptr<Mutex> mutexPtr;
  }
  ```

### ***Summary***

1. *复制`RAII`对象必须一并复制它所管理的资源，所以资源的`copying`行为决定`RAII`对象的`copying`行为*
2. *普遍常见的RAII class copying 行为是：禁止复制、施行引用计数法*

## ***条款15 资源管理类中提供对原始资源的访问***

*这里想要将`RAII`class对象进行转换为其内含之原始资源，有两种转换*

1. *示例代码*
   
   - ```cpp
     FontHandle getFont();
     void releaseFont(FontHandle fh);
     
     class Font {
     public:
       explicit Font(FontHandle fh) : f(fh) { }
     
       ~Font() {
         releaseFont(f);
       }
     
       FontHanle get() const { return f; } // 显示转换
     
       operator FontHandle() const { return f; } // 隐式转换
     private:
       FontHandle f;
     };
     
     Font f1(getFont());
     FontHanle f2 = f1;
     ```

2. *显示转换*
   
   - *提供`get()`函数得到原始指针*
   - *显示调用 很不错 没有啥问题*

3. *隐式转换*
   
   - *允许隐式转换，但是会有问题，在客户`FontHanle f2 = f1;`的时候，并不知道这还有一层隐式转换的逻辑，所以会增加错误率*

### ***Summary***

1. *APIs往往要求访问原始资源，所以每一个RAII class应该提供一个”取得其所管理之资源“的方法*
2. *对原始资源的方法可能会经由显式转换或隐式转换*
   - *相对而言显式转换比较安全，但是频繁`get()`很烦人*
   - *但隐式转换对用户比较方便*
3. *个人觉得是显示调用要好，频繁调用`get()`又不会死，隐式调用用户如果不告诉他，或者他自己发现，也不会察觉到有一次隐式调用，会增加报错的几率*

## ***条款16 new和delete 要使用相同形式***

1. *在使用`new` `delete`需要使用一致的形势使用，否则会导致内存泄漏*
   
   ```cpp
   std::string* stringPtr1 = new std::string;
   std::string* stringPtr2 = new std::string[100];
   ...
   delete stringPtr1;
   delete[] stringPtr2;
   ```

2. *要注意`typedef`自定义类型*
   
   ```cpp
   typedef std::string AddressLines[4];
   std::string* pal = new AddressLines; // new string[4]
   
   delete pal; // undefined!!!
   delete[] pal; // fine
   ```
   
   *这个规则对于使用`typedef`的程序员来说十分重要，因为它意味着`typedef`的作者必须说清楚：当程序员以`new`创建该种`typedef`类型对象是，该以哪一种`delete`形式删除之，考虑一下这个`typedef`:*
   
   *为避免诸如此类错误，尽量不是对数组形式使用`typedef`操作。这容易达成，因为C++标准程序库含有`string` `vector`等templates,可以将数组的需求下降到0*

### ***Summary***

1. *在使用`new` `delete`需要使用一致的形势使用，否则会导致内存泄漏*
2. *在`typedef`自定义类型的时候，一定要注释写着哪一种delete形式删除*

## ***条款17 以独立语句将newed对象置入智能指针***

1. *有些智能指针类(比如`shared_ptr<T>`)不支持隐式类型转换,假设存在这样两个函数*
   
   ```cpp
   void fun(shared_ptr<int> lhs,int rhs);
   int foo();
   ```
   
   *那么对于以下函数调用*
   
   ```cpp
   fun(new int,foo());
   ```
   
   *将不能通过编译,解决方法之一:*
   
   ```cpp
   fun(shared_ptr<int>(new int),foo());
   ```
   
   *它共有三个步骤*
   
   - *执行`new int`*
   - *构造`shared_ptr<int>`*
   - *调用`foo()`*
   
   *但是由于编译器对于同一语句的各项操作具有重新排列的自由,因此除了执行new int肯定在构造`shared_ptr<int>`之前外,调用`foo()`,可以发生在任何阶段,可能在最前,中间,最后,如果是按:执行`new int`→调用`foo()`→构造`shared_ptr<int>`的顺序执行,那么如果`foo()`发生异常,就会在`shared_ptr<int>`构造之前造成内存泄露.*

2. *对于1出现的问题,可以利用编译器对于"跨越语句的各项操作"没有重新排列的权力,以独立语句将newed对象置入智能指针,如下*
   
   ```cpp
   shared_ptr<int> ptr(new int);
   fun(ptr,foo);
   ```
   
   *这样可以防止由于foo在new出的int被放入`shared_ptr<int>`之前抛出异常而导致内存泄露,但要注意:*
   
   - *ptr不是临时对象,也就是说调用fun后ptr管理的内存没有被释放,而ptr的存在并不是为了要访问底层资源,而是为了防止出现异常而造成内存泄露存在的,所以如果不需要ptr所指向的内存,最好调用`reset()`将它释放.(个人认为这个条款有些鸡肋)*

