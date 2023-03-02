# c++ 关键字与关键库函数


## ***前言***

1. *想了想，最好的复习方法其实就是去写出来，good idea~*

## ***strlen V.S sizeof***

1. ***什么是`strlen`***

   - *`strlen` 是头文件 `<cstring>` 中的函数*

   - *`strlen`测量的是字符串的实际长度（其源代码如下）以`\0`结尾*

     ```cpp
     size_t strlen(const char *str) {
         size_t length = 0;
         while (*str++)
             ++length;
         return length;
     }
     ```

2. ***什么是`sizeof`***

   - *`sizeof`是c++里的一个操作符，用来测量对象或者表达式类型所占字节的大小*

     ```cpp
     #include <iostream>
     #include <cstring>
     
     using namespace std;
     
     int main()
     {
         char arr[10] = "hello";
         cout << strlen(arr) << endl; // 5
         cout << sizeof(arr) << endl; // 10
         return 0;
     }
     ```

3. ***两者的不同点***

   - *若字符数组 arr 作为函数的形参，sizeof(arr) 中 arr 被当作字符指针来处理，strlen(arr) 中 arr 依然是字符数组，从下述程序的运行结果中就可以看出*

     ```cpp
     #include <iostream>
     #include <cstring>
     
     using namespace std;
     
     void size_of(char arr[])
     {
         cout << sizeof(arr) << endl; // warning: 'sizeof' on array function parameter 'arr' will return size of 'char*' .
         cout << strlen(arr) << endl; 
     }
     
     int main()
     {
         char arr[20] = "hello";
         size_of(arr); 
         return 0;
     }
     /*
       输出结果：
           8
           5
     */
     ```

   - *`strlen` 本身是库函数，因此在程序运行过程中，计算长度 / 而 `sizeof` 是在编译时计算长度；*

   - *`strlen` 的参数必须是 `char *` 类型的变量 / `sizeof` 的参数可以是类型，也可以是变量，且必须是完整类型*

   - *`strlen` 是一个函数，如果接受表达式则会对表达式进行运算 / sizeof 接受的参数可以是对象也可以是表达式，但是 sizeof(expression) 在运行时不会对接受的表达式进行计算，编译器只会推导表达式的类型从而计算占用的字节大小；*

     ```cpp
       #include <iostream>
       using namespace std;
       int main(int argc, char * argv[])
       {
           int x = 4;
           char *s = "12345678";
           char *p = s;
           sizeof(x++);
           printf("%d\n", x);
           strlen(p++);
           return 0;
       }
     ```

## ***C 和 C++ static 中的作用***

1. ***指路，在`static`的专题有讲解***
   - *https://vlicecream.github.io/c-static/*

## ***const 作用及用法***

1. ***指路，在`const`的专题有讲解***
   - *https://vlicecream.github.io/c-const/*

## ***define V.S const***

1. ***编译阶段***
   - *`define` 是在编译预处理阶段进行替换 / `const` 是在编译阶段确定其值*
2. ***安全性***
   - *`define` 定义的宏常量没有数据类型，只是进行简单的代码替换，不会进行类型安全的检查*
   - *`const` 定义的常量是有类型的，是要进行判断的，可以避免一些低级的错误*
3. ***存储空间***
   - *`define` 定义的宏定义只是作为代码替换的表达式而已，宏定义本身不占用内存空间，`define` 定义的宏常量，在程序中使用多少次就会进行多少次替换，内存中有多个备份，实际使用宏定义替换代码时占用的是代码段的空间*
   - *`const` 定义的常量占用静态存储区的只读空间，程序运行过程中常量只有一份*
4. ***调试***
   - *`define` 定义的宏常量不能调试，因为在预编译阶段就已经进行替换了*
   - *`const` 定义的常量可以进行调试*
5. ***接受参数***
   - *`define` 可以接受参数构造非常复杂的表达式*
   - *`const` 不能接受参数*

## ***inline 作用及使用方法***

1. ***指路，在`inline`专题有讲解***

