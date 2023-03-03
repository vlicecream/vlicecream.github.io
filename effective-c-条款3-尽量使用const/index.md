# 条款3-尽量使用const


## ***修饰指针***

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

## ***修饰 STL-iterator***

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

## ***将某些东西声明为const可以帮助编译器侦测出错误用法***

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

## ***bitwise constness && logical constness***

- *编译器强制实行bitwise constness(又称physical constness,物理上的常量性,即成员函数不更改对象的任何一个bit时才可以说是const),例如*

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

## ***函数重载***

- *如果参数是引用,可以基于参数是否为const实现函数重载(也可以基于指针是否为const实现函数重载),特殊的,对于成员函数,因为它存在一个隐含的this指针参数,因而可以基于函数是否为const实现重载*

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

## ***避免const和非const成员函数中的重复***

- ```cpp
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
      	return const_cast<char&>(
        	static_cast<const TextBlock&>(*this)[position]);
      }
      ...
  };
  ```

  *可以看出,经过了两次类型转换*

  *第一次通过`static_cast`将`*this`转为`const TextBlock&`，以确保调用的是`operator[]`的const版本，否则会调用非const版本导致递归调用造成栈溢出*

  *第二次通过const_cast去掉const版本的opsrator[]返回的const char&的const特性以与函数的返回类型相匹配*

- ***对于const_cast的行为之前存在一些误解,对于以下代码***

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

