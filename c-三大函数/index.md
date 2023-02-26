# 三大函数


***!!!本文基于这个一个 `complex`的类去讲解 操作符重载***

```cpp
class complex {
public:
  complex(double r = 0, double i = 0) : re(r), im(i) {}
  complex& operator+=(const complex&);
  double real() const { return re; }
  double imag() const { return im; }

private:
  double re, im;

  friend complex& __doapl(complex *, const complex &);
};
```

## 三个特殊函数

1. ***构造函数***
2. ***拷贝构造函数***
3. ***析构函数***

```cpp
class String {
public:
  String(const char* cstr = 0);  // 构造函数
  String(const String &str);  // 他是接受自己的东西 所以这是一个拷贝构造 
  String& operator = (const String& str);  // 操作符重载 =右手边也是自己的东西 所以这是拷贝赋值 
  ~String();  // 析构函数 当这个类死亡的时候 就会调用 析构函数
  
  char* get_c_str() const { return m_data; }
private:
  char* m_data
};
```

## 构造函数和析构函数

```cpp
inline String::String(const char* cstr = 0) {
  if (cstr) {
    m_data = new char[strlen(cstr)+1];
    strcpy(m_data, cstr);
  } else {
    m_data = new char[1];
    *m_data = '\0';
  }
}  // c里面字符串是以 '\0' 为结束符号 这是构造函数

inline String::~String() {
  delete[] m_data;
}  // 析构函数 因为上述构造函数 为str分配了一个内存 所以要释放内存 要不然会内存泄漏

// class 有指针 多半要做动态分配 所以就要在析构函数 释放内存
```

## 深拷贝与浅拷贝

```cpp
String a("Hello"); // 这个时候 a 的 data 指向了 'Hello\0' 的地址
String b("World"); // 这个时候 b 的 data 指向了 ‘World\0’ 的地址
b = a; // 这个时候 b 就会指向 'Hello\0' 的地址 a&b都指向了同一个地址 可是 'World\0' 还在  造成内存泄漏 而且你改a b就会受到影响 所以这种 'b = a' 叫做浅拷贝
```

## 拷贝构造函数

```cpp
inline String::String(const String &str) { // 为什么叫构造 因为这是一个构造函数 为什么叫拷贝 因为传参是自己
  m_data = new char[strlen(str.m_data)+1]; // 直接取另一个object的private data
  strcpy(m_data, str.m_data);
}   // 这也是深拷贝
```

## 拷贝赋值函数

```cpp
// 这个函数就是 如果 有 1 a&b 先把 a 清销毁, 2 在new一个内存空间 , 3 在把 b 拷贝到 a 里面来
inline String& String::operator = (const String& str) {
  if (this == &str) {  // 检测自我赋值
    return *this;
  }
  delete[] m_data;  // 1
  m_data = new char[ strlen(str.m_data)+1 ];  // 2
  strcpy(m_data, str.m_data);  // 3
  return *this;
}
// 一定要检测自我赋值 要不然 a&b 同时指向一个内存地址 然后杀掉之后 a&b就指定不到值
```


