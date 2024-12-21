# const


# const

|                                  | const object | non-const object |
|:--------------------------------:|:------------:|:----------------:|
| ***const member functions***     | ✅            | ✅                |
| ***non-const member functions*** | ❌            | ✅                |

## ***object 与 member func 的关系***

1. ***我们根据上述表格来看看例子***
   
   *我们创建了一个常量 str 并且调用`print`方法，但是如果当初设计 `string::print()` 时候没有指明const 那么会直接报错， 因为 "const obj" 调用 non-const 成员函数的时候 是不允许的～～*
   
   ```cpp
   const String str("hello world");
   str.print();
   ```

2. ***我们再来看一个标准库 string 的例子 `class template std::basic_string<...>` 有如下两个 成员函数***
   
   ```cpp
   chatT operator[] (size_type pos) const { ... }  // 不必考虑copy on write
   reference operator[] (size_type pos) { ... }  // 必须考虑copy on write
   ```
   
   *在设计字符串的时候 因为字符串是共享的 可能a拷贝一份 b拷贝一份 就有多份 最致命的是 如果a修改了 b的字符串也随之修改了，所以我们必须要考虑这个共享的问题*
   
   *所以这个就考虑到了 const 下面没有const，那么可能就会修改字符串，上面的有 const 那么就不允许修改*
   
   *下面也是可行的 因为 non-const member func 是可以创建 non-const obj 的*
   
   *但是上面的 const member func 又可以创建 const obj 又可以创建 non-const obj，那么编译器怎么调用呢*
   
   *当成员函数的 “const" 和 "non-const" 版本同时存在 const obj 只能调用const 版本 “non-const obj” 只能调用"non-const"版本*

## ***const修饰***

1. ***const 修饰基本数据类型***
   
   - *基本数据类型，修饰符 const 可以用在类型说明符前，也可以 用在类型说明符后，其结果是一样的。在使用这些常量的时候，只要不改变这些常量的值即可*

2. ***const 修饰指针变量和引用变量***
   
   ```cpp
   const int* p1; //指向整形常量的指针，它指向的值不能修改
   int* const p2; //指向整形的常量指针 ，它不能在指向别的变量，但指向(变量)的值可以修改。
   const int* const p3; //指向整形常量的常量指针 。它既不能再指向别的常量，指向的值也不能修改。
   ```

3. ***const 应用到函数中***
   
   - ***作为参数的 const 修饰符：*** *调用函数的时候，用相应的变量初始化const常量，则在函数体中，按照const所修饰的部分进行常量化，保护了原对象的属性*
     
     ***注意：参数 const 通常用于参数为指针或引用的情况***
   
   - ***作为函数返回值的 const 修饰符：*** *声明了返回值后，const 按照"修饰原则"进行修饰，起到相应的保护作用*

4. ***const 在类中的用法***
   
   - ***const成员变量*** 
     - *只在某个对象生命周期内是常量，而对于整个类而言 是可以改变的*
     - *const 数据成员的初始化只能在类的构造函数的初始化列表中进行*
       - *因为类可以创建多个对象，不同的对象其 const 数据成员值可以不同。所以不能在类的声明中初始化 const 数据成员，因为类的对象在没有创建时候，编译器不知道 const 数据成员的值是什么*
   - ***const成员函数***
     - *const 成员函数的主要目的是防止成员函数修改对象的内容*
     - *要注意，const 关键字和 static 关键字对于成员函数来说是不能同时使用的*
       - *因为 static 关键字修饰静态成员 函数不含有 this 指针，即不能实例化，const 成员函数又必须具体到某一个函数*

5. ***const 修饰类对象，定义常量对象***
   
   - *常量对象只能调用常量函数，别的成员函数都不能调用*

6. **C++** 中的 **const**类成员函数(用法和意义)
   
   - *常量对象可以调用类中的 const member func，但不能调用non-const member func*
     
     *这是因为对象调用成员函数的时候，在形参列表最前面加一个形参`this`，但是这个是隐式的。this 指针是默认指向调用函数的当前对象的，所以，很自然，this是一个常量指针`test* const`，因为不可修改this指针代表的地址。但当member func的参数列表后加了 const 关键字 `void print() const;` ，此成员函数为常量成员函数，此时他的隐式this形参为 `const test* const` 即 **不可以通过this指针来改变指向对象的值***
   
   - *非常量对象可以调用类中的 const 成员函数，也可以调用非 const 成员函数。*

## ***Effective c++ Item***

1. *对于更多的const的相关知识，可以查看《[Effective c++ Item3 - 尽量使用const](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE1-4-%E4%B9%A0%E6%83%AFc-/)》*

