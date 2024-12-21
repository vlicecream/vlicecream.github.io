# 编写头文件的思路流程(reference)


# 编写头文件的思路流程(reference)

## ***class内***

1. ***编写防卫式声明***
   
   - ```cpp
     #ifndef __STRING__  // __STRING__    自由取
     #define __STRING__  // __STRING__    自由取
     #endif
     ```

2. ***编写class head***
   
   - ```cpp
     #ifndef __STRING__  // __STRING__    自由取
     #define __STRING__  // __STRING__    自由取
     
     class String {
     
     };
     
     #endif
     ```

3. ***编写数据 放入private***
   
   - ```cpp
     #ifndef __STRING__  // __STRING__    自由取
     #define __STRING__  // __STRING__    自由取
     
     class String {
     private:
       char* m_data;
     };
     
     #endif // __STRING__
     ```

4. ***编写构造函数***
   
   - *类的名称要相同*
   
   - *有哪些参数要传，需不需要默认值，参数传递是 pass by value 还是 pass by reference*
   
   - *合理利用初始列，初始列就是设初始值*
   
   - *`{ }` 内可能会有内存分配哦~*
   
   - *const!!!*
     
     ```cpp
     #ifndef __STRING__ // __STRING__    自由取
     #define __STRING__ // __STRING__    自由取
     
     class String {
     public:
       String(const char* cstr = 0); // 构造函数
     private:
       char* m_data;
     };
     
     #endif // __STRING__
     ```

5. ***设计三大函数***
   
   - ```cpp
     #ifndef __STRING__ // __STRING__    自由取
     #define __STRING__ // __STRING__    自由取
     
     class String {
     public:
       String(const char* cstr = 0); // 构造函数
         String(const String& str);  // 拷贝构造
       String& operator=(const String& str); // 拷贝赋值
       ~String();  // 析构函数
     private:
       char* m_data;
     };
     
     #endif // __STRING__
     ```

6. ***设计其他函数***
   
   - *当我们设计一个函数的时候，一定要思考，要不要加const*
     *到底要不要加，只需要看，这个参数会不会被改变，如果不会改变或不能改变，则加const*
     *结合此处代码考虑，取出和构造的拷贝的时候，都不会改变str，所以就加一个const*
     
     ```cpp
     class String {
     public:
       String(const char* cstr = 0); // 构造函数
         String(const String& str);  // 拷贝构造
       String& operator=(const String& str); // 拷贝赋值
       ~String();  // 析构函数
       char* get_c_str() const { return m_data; }  // 拿到私有的数据
     private:
       char* m_data;
     };
     
     #endif // __STRING__
     ```

## ***class外***

1. ***构造函数和析构函数***
   
   - ```cpp
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

2. ***拷贝构造函数***
   
   - ```cpp
     // 拷贝构造没有返回值
     inline String::String(const String& str) {
       m_data = new char[ strlen(str.m_data) + 1 ];
       strcpy(m_data, str.m_data);
     }
     ```

3. ***拷贝赋值函数***
   
   - ```cpp
     inline String& String::operator=(const String& str) { // 这一行与下一行的&不一样 第一行是引用(reference) 第二行是取"str"地址
       if (this == &str) return *this; // 判断是不是自我赋值
       delete[] m_data;
       m_data = new char [ strlen(str.m_data) + 1];
       strcpy(m_data, str.m_data);
       return *this;
     }
     ```

