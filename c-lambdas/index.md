# lambdas


## ***引言***

1. *c++ 11引入了`lambdas`，允许定义内联函数，可以作为参数或局部对象使用*
2. *Lambdas改变了c++标准库的使用方式*
3. *`lambda`是可以在语句和表达式中定义的功能定义。因此，您可以将lambda用作内联函数。最小lambd函数没有参数，只是简单地做一些事情*

## ***lambdas***

1. ***怎么去定义一个`lambdas`***

   - ```cpp
     [] {
       std::cout << "hello lambdas" << std::endl;
     }
     ```

     *对于上述代码，我们可以写成这个样子*

     ```cpp
     [] {
       std::cout << "hello lambdas" << std::endl;
     } (); // prints "hello lambdas"
     ```

     *那我们改进后 看着也有点怪怪的，我们大费周章就为了一个cout，干嘛呢，假如要100次`cout`还不如抽出来 然后调用100次呢，所以多半我们会这么写*

     ```cpp
     auto l = [] {
       std::cout << "hello lambdas" << std::endl;
     };
     ...
     l(); // prints "hello lambdas"
     ```

     *`lambda`是可以在语句和表达式中定义的功能定义。因此，您可以将lambda用作内联函数。最小lambd函数没有参数，只是简单地做一些事情，所以跟上述的不太一样，lambda是可以不用取名字，直接在语句或者表达式中定义，用完就直接不管的*

2. ***`lambdas`的模板***

   - *`[capture list] (parameter list) mutable throwSpec -> returnType {...}`*

   - *`mutable` `throwSpec`  `retType` 都是可选项，但是只要有其中一个，就必须带前面的`()`*

   - *`mutable`是可变的，如果你想修改传入的值，就必须带一个`mutable`*

   - *`capture list` 捕获列表，指`lambda`所在函数中定义的局部变量的列表，定义在与 `lambda` 函数相同作用域的参数引用也可以被使用，一般被称作 `closure`（闭包），以下为闭包的常见用法*

     ```cpp
     []      // 没有定义任何变量。使用未定义变量会引发错误。
     [x, &y] // x pass by value（默认），y pass by reference。
     [&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
     [=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
     [&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
     [=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。
     ```

   - *`return type` 返回值类型*

   - *`parameter list` 参数列表*

   - *`returnType` 返回参数*

## 通过代码来看lambda

1. ***pass by value && pass by reference***

   - ```cpp
     int id = 0;
     auto f = [id] () mutable {
       std::cout << id << std::endl;
       ++id;
     };
     
     id = 42;
     f();
     f();
     f();
     std::cout << id << std::endl;
     
     // 输出
     0
     1
     2
     42
     ```

   - ```cpp
     int id = 0;
     auto f = [&id] (int param) {
       std::cout << id << std::endl;
       ++id; ++param;
     };
     
     id = 42;
     f(7);
     f(7);
     f(7);
     std::cout << id << std::endl;
     
     // 输出
     42
     43
     44
     45
     ```

2. ***mutable才能修改***

   - ```cpp
     int id = 0;
     auto f = [id] () {
       std::cout << id << std::endl;
       ++id; // error: increment of read-only variable 'id';
     };
     ```

