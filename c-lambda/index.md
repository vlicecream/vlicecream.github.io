# lambda


## ***lambda***

1. *怎么去定义一个`lambda`*
   
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

2. *`lambdas`的模板*
   
   - ```cpp
     [ capture-list ] ( params ) mutable(optional) constexpr(optional)(c++17) exception attribute -> ret { body }
     
     // 可选的简化语法
     [ capture-list ] ( params ) -> ret { body } [ capture-list ] ( params ) { body }
     [ capture-list ] { body }
     ```
   
   - *capture-list: 捕捉列表，这个不用多说，前面已经讲过，它不能省略*
     
     ```cpp
     []      // 没有定义任何变量。使用未定义变量会引发错误。
     [x, &y] // x pass by value（默认），y pass by reference。
     [&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
     [=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
     [&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
     [=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。
     ```
   
   - *params:参数列表，可以省略(但是后面必须紧跟函数体)*
   
   - *mutable:可选，将 lambda 表达式标记为 mutable 后，函数体就可以修改传值方式捕获的变量*
   
   - *constexpr:可选*
   
   - *exception:可选，指定*
   
   - *attribute:可选，指定* 
   
   - *ret:可选，返回值类型* 
   
   - *body:函数执行体*

## ***lambda 的本质***

1. *最前边的 **[]** 是 lambda 表达式的一个很重要的功能，就是 **闭包***
   
   *先说明一下 lambda 表达式的大致原理*
   
   * 每当你定义一个 lambda 表达式后，编译器会自动生成一个匿名类，这个类当然重载载了`()`运算符，我们称为闭包类型*
   
   *那么在运行时，这个 lambda 表达式就会返回一个匿名的闭包实例，其实是一个右值*
   
   *所以，我们上面的 lambda 表达式的结果就是一个个闭包实例*
   
   *闭包的一个强大之处是其可以通过传值或者引用的方式捕捉其封装作用域内的变量，前面的方括号就是用来定义捕捉模式以及变量，我们又将其称为 lambda 捕捉块。例子如下*
   
   ```cpp
   int main() {
     int x = 10;
     auto add_x = [x](int a) { return a + x; }; // 复制捕捉x,lambda 表达式无法修改此变量
     auto multiply_x = [&x](int a) { return a * x; }; // 引用捕捉x，lambda 表达式可以修改此变量
     cout << add_x(10) << " " << multiply_x(10) << endl; // 输出:20 100
     return 0;
   }
   ```

2. *而 lambda 表达式一个更重要的应用是其可以用于函数的参数，通过这种方式可以实现回调函数*
   
   *其实，最常用的是在STL算法中，比如你要统计一个数组中满足特定条件的元素数量， 通过 lambda 表达式给出条件，传递给 count_if 函数*
   
   ```cpp
   int val = 3;
   vector<int> v {1, 8, 5, 3, 6, 10};
   int count = std::count_if(v.beigin(), v.end(), [val](int x) { return x > val; });
   // v中大于3的元素数􏰁
   ```

## ***Summary***

1. *c++ 11引入了`lambda`，允许定义内联函数，可以作为参数或局部对象使用*
2. *从本质上来讲，表达式只是一种语法糖，因为所有其能完成的工作都可以用其它稍微复杂的代码来实现，但是它简便的语法却给 C++ 带来了深远的影响*
3. *从广义上说， 表达式产生的是函数对象。*
   - *在类中，可以重载函数调用运算符`()`，此时类的对象可以将具有类似函数的行为，我们称这些对象为函数对象或者仿函数*
   - *相比 lambda表达式，函数对象有自己独特的优势*
4. *`lambda`是可以在语句和表达式中定义的功能定义。因此，您可以将`lambda`用作内联函数。最小`lambda`函数没有参数，只是简单地做一些事情*
5. *要弄清楚lambda的模板并且分清楚"[ ]"的传值使用，想要修改值就pass by reference + mutable*

