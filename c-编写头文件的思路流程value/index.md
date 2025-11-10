# 编写头文件的思路流程(value)


## ***class内***

1. ***编写防卫式声明***
   
   - ```cpp
     #ifndef __COMPLEX__  // __COMPLEX__    自由取
     #define __COMPLEX__  // __COMPLEX__    自由取
     #endif
     ```

2. ***编写class head***
   
   - ```cpp
     #ifndef __COMPLEX__  // __COMPLEX__    自由取
     #define __COMPLEX__  // __COMPLEX__    自由取
     
     class complex {
     
     };
     
     #endif
     ```

3. ***编写数据 放入private***
   
   - ```cpp
     #ifndef __COMPLEX__  // __COMPLEX__    自由取
     #define __COMPLEX__  // __COMPLEX__    自由取
     
     class complex {
     private:
       double re, im;
     };
     
     #endif // __COMPLEX__
     ```

4. ***编写构造函数***
   
   - *类的名称要相同*
   
   - *有哪些参数要传，需不需要默认值，参数传递是 pass by value 还是 pass by reference*
   
   - *合理利用初始列，初始列就是设初始值*
   
   - *`{ }` 内可能会有内存分配哦~*
     
     ```cpp
     #ifndef __COMPLEX__ // __COMPLEX__    自由取
     #define __COMPLEX__ // __COMPLEX__    自由取
     
     class complex {
     public:
       complex(double r = 0, double i = 0) : re(r), im(i) {}
     
     private:
       double re, im;
     };
     
     #endif // __COMPLEX__
     ```

5. ***设计"+="函数***
   
   - *这里需要考虑是否为成员函数，如果是则在class内定义，如果不是则在class内定义，这里选择是*
     
     ```cpp
     #ifndef __COMPLEX__ // __COMPLEX__    自由取
     #define __COMPLEX__ // __COMPLEX__    自由取
     
     class complex {
     public:
       complex(double r = 0, double i = 0) : re(r), im(i) {}
       complex& operator+=(const complex&);
     
     private:
       double re, im;
     };
     
     #endif // __COMPLEX__
     ```

6. ***设计其他函数***
   
   - *当我们设计一个函数的时候，一定要思考，要不要加const*
     *到底要不要加，只需要看，这个参数会不会被改变，如果不会改变或不能改变，则加const*
     *结合此处代码考虑 real imag 是取出 re im 没有改动 所以 要加const *
     
     ```cpp
     #ifndef __COMPLEX__ // __COMPLEX__    自由取
     #define __COMPLEX__ // __COMPLEX__    自由取
     
     class complex {
     public:
       complex(double r = 0, double i = 0) : re(r), im(i) {}
       complex& operator+=(const complex&);
       double real() const { return re; }
       double imag() const { return im; }
     
     private:
       double re, im;
     };
     
     #endif // __COMPLEX__
     ```

7. ***想拿私有数据，我们可以交个朋友***
   
   - ```cpp
      #ifndef __COMPLEX__ // __COMPLEX__    自由取
      #define __COMPLEX__ // __COMPLEX__    自由取
       
      class complex {
      public:
        complex(double r = 0, double i = 0) : re(r), im(i) {}
        complex& operator+=(const complex&);
        double real() const { return re; }
        double imag() const { return im; }
       
      private:
        double re, im;
        friend complex& __doapl(complex*, const complex&);
      };
       
      #endif // __COMPLEX__
     ```

## ***class外***

1. ***接口部分***
   
   - ```cpp
     inline complex& complex::operator += (const complex& r)
     
     inline complex& __doapl(complex* ths, const complex& r)
     /*
         思考 参数 / const / pass by reference / return by reference / return type / class之外 inline
     */
     ```

2. ***定义部分***
   
   - ```cpp
     inline complex& complex::operator += (const complex& r) {
       return __doapl(this, r);
     }
     
     inline complex& __doapl(complex* ths, const complex& r) {
       ths->re += r.re;
       ths->im += r.im;
       return *ths;
     }
     ```

3. ***"+" 非成员函数***
   
   - *为什么 "+" 要写为非成员函数 因为 "+" 其实有很多情况 比如复数+复数 复数+实数等*
   
   - ```cpp
     inline complex operator + (const complex& x, const complex& y) {
       return complex( real(x) + real(y), imag(x) + imag(y) );
     }
     /*
         思考 返回类型 参数类型 
     */
     ```

