# 

# 左值/右值/左值引用/右值引用


## ***前言***

1. *此篇博客会按照一个逻辑推导 带你们区分到底什么是 左值/右值/左值引用/右值引用*

## ***逻辑推导1***

*我们来看这一行简单的代码*

```cpp
int i = 10;
```

***i 在左边 -> 左值 / 10 在右边 -> 右值*** *这句话，在这行代码是适用的，但是！这句话不准确*

*我们看 i 在内存中是有位置的实际变量 / 而 10 没有存储地址，也就是在内存中是没有职位的*

*所以我们不能说 `10 = i;`，因为10是没有位置的，不能存储数据*

*但是 i 是一个左值，我们可以用另一个变量a 等于 i*

```cpp
int a = i;
```

*这就是为什么说 "i 在左边->左值 / 10 在右边->右值" 这句话是不准确的*

## ***逻辑推导2***

*现在我们来加一个函数*

```cpp
int GetValue()
{
  return 10;
}
```

*随后调用这个函数*

```cpp
int i = GetValue();
```

*在这个例子中，GetValue返回了一个临时值，也就是右值，然后将这个右值保存到左值*

*当然因为这是一个右值，所以我们是这给这个右值赋值，那么就会失败*

```cpp
GetValue() = 10; // Error!!!
```

*我们在试着修改一下，如果将函数返回值改成返回左值，那就变得很有趣了*

```cpp
int& GetValue() { }
```

*将"int"加上"&"就变成了左值引用 这时候需要为我的值提供某种存储空间 比如一个静态int，然后返回他*

```cpp
int& GetValue()
{
  static int value = 10;
  return value;
}

int main() {
  int i = GetValue();
  GetValue() = 10; // true! 我就可以给他赋值，这个表达式也没有啥问题了，这就是左值引用
  return 0;
}
```

## ***逻辑推导3***

*我们再再再展开看一下，现在再加了一个`SetValue()`*

```cpp
void SetValue(int value) { }

int main() 
{
  int i = value;
  SetValue(i); // true!
  SetValue(10); // true
}
```

*当函数调用时，传左值是可以的，传右值也是可以的，因为这个右值会被用来创建一个左值*

*So? 我们可以马上看出，哪个变量是临时的，哪个不是，这个规则就是你不能将右值赋给左值引用*

*我们可以很容易的去检查这个，如果我在 int 加一个 &，现在我在取一个 referebce to int，这是一个左值引用，`SetValue(10)`马上这个就会error*

***因为非const引用的初始值必须是左值***

*所以 const int& i = 10; 这个是一个特殊的规则*

*实际情况就是 编译器可能会用你的存储创建一个临时变量，然后赋值给那个引用*

`const int& i = 10;` ===>` int temp = 10;`  `const int& a = temp;`

*所以实际上 还是不可避免的创建了一个左值 但是也同时支持了左值右值*

## 题目

*好！ 讲到了这里 来出道题目 来看看你们到底懂得了怎么区分左右值*

*题目: 请找出 所有的左值 和 所有的右值!*

```cpp
int main()
{
  std::string firstName = "lin";
  std::string secondName = "ting";
  std::string fullName = firstName + secondName;
}
```

*答案*

```cpp
/*
  左值: firstName/secondName/fullName
  右值: "lin"/"ting"/firstName + secondName

  "firstName + secondName" 为什么是右值
    因为 两个字符串相加是一个临时变量 所以他是临时值 也就是右值
*/























```

## 逻辑推导4

*好 我们再次进行拓展*

```cpp
void PrintName(std::string& name)
{
  std::cout << name << std::endl;
}

int main()
{
  std::string firstName = "lin";
  std::string secondName = "ting";
  std::string fullName = firstName + secondName;
  
  PrintName(firstName); // true!!
  PrintName(secondName); // true!!
  PrintName(firstName + secondName); // error!! 因为他是一个右值
}
```

*这就是为什么会看到很多用c++写的常量引用 因为他支持左右值，所以我们可以通过这种方法来检测哪些是左值 哪些是右值*

*那我们有没有办法写一个函数，只接受临时对象，肯定是有的，那就是右值引用*

*右值引用跟左值引用差不多，只不过多了一个"&"符号*

```cpp
void PrintName(std::string&& name)
{
  std::cout << name << std::endl;
}

int main()
{
  std::string firstName = "lin";
  std::string secondName = "ting";
  std::string fullName = firstName + secondName;
  
  PrintName(firstName); // error!! 右值不能绑定到左值
  PrintName(firstName + secondName); // true!!
}
```

*这就说得通了！这很coooool，因为这意味着我们可以写函数重载，他只接受临时对象*

```cpp
void PrintName(const std::string& name)
{
  std::cout << name << std::endl;
} 
  
void PrintName(std::string&& name)
{
  std::cout << name << std::endl;
}

int main()
{
  std::string firstName = "lin";
  std::string secondName = "ting";
  std::string fullName = firstName + secondName;
  
  PrintName(firstName); // true!!
  PrintName(firstName + secondName); // true!!
}
```

*那么他有啥作用呢*

***作用非常大 尤其是在移动语义方面 当然 我这里不会讲这个***

*这里代码的主要优势是优化，如果我们传入的是一个临时对象，那么我们就不需要担心他们是否活着 是否完整 是否拷贝，我们可以简单的偷他的资源 给到特定对象 或者其他地方使用它们，因为我们知道他是暂时的 他不会存在很长时间， 而如果你传入的不是右值，除了他是const之外 你不能从这个左值中窃取任何东西，因为他可能会在很多函数中使用*

## ***总结***

1. ***左值是有某种存储支持的变量***
2. ***右值是临时值***
3. ***左值引用仅仅接受左值 除了const***
4. ***右值引用仅仅接受右值***


