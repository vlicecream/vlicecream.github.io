# inline


# inline

## ***什么是内联函数-inline***

1. *引出*
   
   - *函数是一个可以重复使用的代码块，CPU 会一条一条地挨着执行其中的代码。CPU 在执行主调函数代码时如果遇到了被调函数，主调函数就会暂停，CPU 转而执行被调函数的代码；被调函数执行完毕后再返回到主调函数，主调函数根据刚才的状态继续往下执行*
   - *一个 C/C++程序的执行过程可以认为是多个函数之间的相互调用过程，它们形成了一个或简单或复杂的调用链条，这个链条的起点是 main()，终点也是 main()。当 main() 调用完了所有的函数，它会返回一个值（例如`return 0;`）来结束自己的生命，从而结束整个程序*
   - *函数调用是有时间和空间开销的。程序在执行一个函数之前需要做一些准备工作，要将实参、局部变量、返回地址以及若干寄存器都压入栈中，然后才能执行函数体中的代码；函数体中的代码执行完毕后还要清理现场，将之前压入栈中的数据都出栈，才能接着执行函数调用位置以后的代码*
   - *如果函数体代码比较多，需要较长的执行时间，那么函数调用机制占用的时间可以忽略；如果函数只有一两条语句，那么大部分的时间都会花费在函数调用机制上，这种时间开销就就不容忽视*
   - *为了消除函数调用的时空开销，C++ 提供一种提高效率的方法，即在编译时将函数调用处用函数体替换，类似于C语言中的宏展开。这种在函数调用处直接嵌入函数体的函数称为内联函数（Inline Function），又称内嵌函数或者内置函数*

2. *怎么定义内联函数*
   
   - ```cpp
     #include <iostream>
     using namespace std;
     //内联函数，交换两个数的值
     inline void swap(int *a, int *b){
         int temp;
         temp = *a;
         *a = *b;
         *b = temp;
     }
     int main(){
         int m, n;
         cin>>m>>n;
         cout<<m<<", "<<n<<endl;
         swap(&m, &n);
         cout<<m<<", "<<n<<endl;
         return 0;
     }
     ```

3. *内联函数要注意的点*
   
   - *要在函数定义处添加 inline 关键字，在函数声明处添加 inline 关键字虽然没有错，但这种做法是无效的，编译器会忽略函数声明处的 inline 关键字*
   - *对函数作 inline 声明只是程序员对编译器提出的一个建议，而不是强制性的，并非一经指定为 inline 编译器就必须这样做。编译器有自己的判断能力，它会根据具体情况决定是否这样做*

4. *内联函数的坑*
   
   - *使用内联函数的缺点也是非常明显的，编译后的程序会存在多份相同的函数拷贝，如果被声明为内联函数的函数体非常大，那么编译后的程序体积也将会变得很大，所以再次强调，一般只将那些短小的、频繁调用的函数声明为内联函数*

## ***inline 工作原理***

1. *inline 不是在调用时发生控制转移关系，而是在编译阶段将函数体嵌入到每一个调用该函数的语句块中，编译器会将程序中出现inline的调用表达式用内联函数的函数体来替换，普通函数调用时，需要切换栈帧寄存器，同时栈中压入参数，返回值，然后跳转，这些都需要开销，而内联函数不要这些开销，直接将内联函数中函数体直接插入或者替换到该函数调用点*
2. *普通函数是将程序执行转移到被调用函数所存放的内存地址，当函数执行完后，返回到执行此函数前的地方。转移操作需要保护现场，被调函数执行完后，再恢复现场，该过程需要较大的资源开销*
3. *虽然内联函数在调用时直接进行展开，但实际在编译后代码中存在内联函数的定义，可以供编译器进行调用。普通函数可以有指向它的函数指针，内敛函数也可以有指向它的函数指针*

## ***inline的优点***

1. *不会产生函数调用开销。节省了调用函数时在堆栈上推送/弹出变量的开销。节省了函数返回调用的开销。*
2. *当你inline一个函数的时候，你可以让编译器对函数体执行上下文特定的优化，其他优化可以通过考虑调用上下文和被调用上下文的流程来获得，而对于普通函数不会有这种优化*

## ***inline的缺点***

1. *从内联函数中添加的变量会消耗额外的寄存器，在内联函数之后，如果要使用寄存器的变量数量增加，则可能会在寄存器变量资源利用方面产生开销，在函数调用点替换内联函数体时，函数使用的变量总数也会增加，用于存储变量的寄存器数量也会增加*
2. *如果你使用太多的内联函数，那么二进制可执行文件的大小会很大，因为相同的代码重复*
3. *过多的内联也会降低指令缓存命中率，从而降低从缓存内存到主内存的指令获取速度*
4. *如果有人更改内联函数内的代码，内联函数可能会增加编译时间开销，那么所有调用位置都必须重新编译，因为编译器需要再次替换所有代码，否则它将继续使用旧功能*
5. *内联函数可能会导致抖动，因为内联可能会增加二进制可执行文件的大小。内存抖动会导致计算机性能下降*

## ***inline的使用场景***

1. *内联函数一般只适用于比较短小，处理较为简单的函数。内联只是对编译器的请求，而不是命令。编译器可以忽略内联请求。编译器可能不会在以下情况下执行内联*
   - *如果函数包含循环（`for, while, do-while`）；*
   - *如果一个函数包含静态变量；*
   - *如果一个函数是递归的；*
   - *如果函数返回类型不是 `void`，并且函数体中不存在 `return` 语句；*
   - *如果函数包含 `switch` 或 `goto` 语句；*

## ***c++内联函数也可以用来代替宏***

1. *先来讲讲宏*
   
   - *使用宏的一个经典例子是求一个数的平方，如下所示：*
     
     ```cpp
     #include <iostream>
     using namespace std;
     #define SQ(y) y*y
     
     int main(){
         int n, sq;
         cin>>n;
         sq = SQ(n);
         cout<<sq<<endl;
         return 0;
     }
     // 运行结果
       9↙
       81
     ```
     
     *看运行结果是没有啥问题的，但是当我们将宏调用 SQ(n) 换成 SQ(n+1) 后，就会出现意想不到的状况*
     
     ```cpp
     #include <iostream>
     using namespace std;
     #define SQ(y) y*y
     
     int main(){
         int n, sq;
         cin>>n;
         sq = SQ(n+1);
         cout<<sq<<endl;
         return 0;
     }
     
     // 运行结果
       9↙
     
       19
     ```
     
     *我们期望的结果是 100，但这里却是 19，两者大相径庭。这是因为，宏展开仅仅是字符串的替换，不会进行任何计算或传值，上面的 `sq = SQ(n+1)`; 在宏展开后会变为`sq = n+1*n+1`;，这显然是没有道理的*
     
     *如果希望得到正确的结果，应该将宏定义改为如下的形式：*
     
     ```cpp
     #define SQ(y) (y)*(y)
     ```
     
     *这样宏调用 `sq = SQ(n+1)`; 就会展开为 `sq = (n+1)*(n+1)`;，得到的结果就是 100。*
     
     *如果你认为这样就万事大吉了，那下面的结果会让你觉得考虑不周：*
     
     ```cpp
     #include <iostream>
     using namespace std;
     #define SQ(y) (y)*(y)
     
     int main(){
         int n, sq;
         cin>>n;
         sq = 200 / SQ(n+1);
         cout<<sq<<endl;
         return 0;
     }
     
     9↙
     200
     ```
     
     *之所以会出现这么奇怪的结果，是因为宏调用 `sq = 200 / SQ(n+1)`; 会被展开为`sq = 200 / (n+1) * (n+1)`;，当 n 被赋值 9 后，相当于 `sq = 200 / 10 * 10`，结果显然是 200*
     
     *要想得到正确的结果，还应该对宏加以限制，在两边增加 ( )，如下所示：*
     
     ```cpp
     #define SQ(y) ( (y)*(y) )
     ```
     
     *这样宏调用 `sq = 200 / SQ(n+1)`; 就会展开为`sq = 200 / ( (n+1) * (n+1) )`;，得到的结果就是 2*
     
     *说了这么多，我最终想强调的是，宏定义是一项 “细思极密” 的工作，一不小心就会踩坑，而且不一定在编译和运行时发现，给程序埋下隐患*

2. *如果在 1 的基础上将宏替换成 inline函数呢*
   
   - ```cpp
     #include <iostream>
     using namespace std;
     inline int SQ(int y){ return y*y; }
     int main(){
         int n, sq;
         cin>>n;
         //SQ(n)
         sq = SQ(n);
         cout<<sq<<endl;
         //SQ(n+1)
         sq = SQ(n+1);
         cout<<sq<<endl;
         //200 / SQ(n+1)
         sq = 200 / SQ(n+1);
         cout<<sq<<endl;
         return 0;
     }
     
     9↙
     81
     100
     2
     ```
     
     *看，一切问题迎刃而解！发生函数调用时，编译器会先对实参进行计算，再将计算的结果传递给形参，并且函数执行完毕后会得到一个值，而不是得到一个表达式，这和简单的字符串替换相比省去了很多麻烦，所以在编写 C++ 代码时我推荐使用内联函数来替换带参数的宏*

3. *使用inline函数替换宏的优点*
   
   - *和宏一样，内联函数可以定义在头文件中（不用加 static 关键字），并且头文件被多次`#include` 后也不会引发重复定义错误。这一点和非内联函数不同，非内联函数是禁止定义在头文件中的，它所在的头文件被多次`#include` 后会引发重复定义错误*
   - *内联函数在编译时会将函数调用处用函数体替换，编译完成后函数就不存在了，所以在链接时不会引发重复定义错误。这一点和宏很像，宏在预处理时被展开，编译时就不存在了。从这个角度讲，内联函数更像是编译期间的宏*

## ***inline的规范***

1. *inline 是一种"用于实现的关键字"，而不是一种"用于声明的关键字"*
   
   - *inline 关键字可以只在函数定义处添加，也可以只在函数声明处添加，也可以同时添加；但是在函数声明处添加 inline 关键字是无效的，编译器会忽略函数声明处的 inline 关键字*
   - *更为严格地说，内联函数不应该有声明，应该将函数定义放在本应该出现函数声明的地方，这是一种良好的编程风格*

2. *将内联函数的声明和定义分散到不同的文件中会出错*
   
   - *在多文件编程中，我们通常将函数的定义放在源文件中，将函数的声明放在头文件中，希望调用函数时，引入对应的头文件即可，我们鼓励这种将函数定义和函数声明分开的做法。但这种做法不适用于内联函数，将内联函数的声明和定义分散到不同的文件中会出错，请看下面的例子*
   
   - *main.cpp 代码*
     
     ```cpp
     #include <iostream>
     using namespace std;
     //内联函数声明
     void func();
     int main(){
         func();
         return 0;
     }
     ```
   
   - *module.cpp 代码*
     
     ```cpp
     #include <iostream>
     using namespace std;
     //内联函数定义
     inline void func(){
         cout<<"inline function"<<endl;
     }
     ```
   
   - *上面的代码能够正常编译，但在链接时会出错。func() 是内联函数，编译期间会用它来替换函数调用处，编译完成后函数就不存在了，链接器在将多个目标文件（.o或.obj文件）合并成一个可执行文件时找不到 func() 函数的定义，所以会产生链接错误*

3. *内联函数虽然叫做函数，在定义和声明的语法上也和普通函数一样，但它已经失去了函数的本质*
   
   - *函数是一段可以重复使用的代码，它位于虚拟地址空间中的代码区，也占用可执行文件的体积，而内联函数的代码在编译后就被消除了，不存在于虚拟地址空间中，没法重复使用*

4. *规范小结*
   
   - *内联函数看起来简单，但是有很多细节需要注意，从代码重复利用的角度讲，内联函数已经不再是函数了。我认为将内联函数作为带参宏的替代方案更为靠谱，而不是真的当做函数使用*
   - *在多文件编程时，我建议将内联函数的定义直接放在头文件中，并且禁用内联函数的声明（声明是多此一举）*

## ***Effective c++ Item***

1. *对于更多的inline知识请看《[Effective c++ Item30](https://vlicecream.github.io/effective-c-%E6%9D%A1%E6%AC%BE26-31-%E5%AE%9E%E7%8E%B0/)》*

