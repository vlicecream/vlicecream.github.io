# 条款4-对象使用前应该被初始化


## ***class的成员变量总是以其声明的次序被初始化***

- *在下述代码中，我们虽然在构造函数的初始化列表中先初始化了`b_`，再是`a_`，但是实际上是以声明的次序被初始化，也就是先初始化 `a_`，在初始化`b_`*

  ```cpp
  class C {
  public:
    C(std::string aName, int bValue) : b_(bValue), a_(aName, b_.v_) { }
  private:
    A a_; // 不展示A class了
    B b_; // 不展示B class了
  }
  ```

- *类类型的构造函数对于成员按:**基类成员→派生类类类型成员(按声明顺序)→派生类内置类型成员(按声明顺序)的顺序进行初始化**.*

## ***构造函数成员初始化列表优于函数体赋值***

- *Foo函数如下*

  ![Foo](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041320336.png)

- *随后我们来看看 不用构造函数的初始化列是什么情况*

  ```cpp
  class Test {
  public:
    explicit Test(const Foo& foo) {
      foo_ = foo;
    }
  private:
    Foo foo_;
  }
  
  int main() {
    Foo foo(1, "foo1");
    Test test(foo);
    return 0;
  }
  ```

  *我们来看看不用构造函数的初始化列 到底做了什么*

  ![不用构造函数的初始化列](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041323781.png)

- *最后我们再来看看用了构造函数的初始化列，形成一个对比*

  ```cpp
  class Test {
  public:
    explicit Test(const Foo& foo) : foo_(foo) { }
  private:
    Foo foo_;
  }
  ```

  ![使用了构造函数的初始化列](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041325435.png)

- *我们也是很明显的发现 使用初始化列 明显性能要高于 不使用初始化列，不使用初始化列要先两次构造函数了，然后一步调用拷贝赋值，而使用初始化列只做了一个构造函数和一次拷贝构造函数*

- ***总结：***

  - ***构造函数对成员进行初始化的动作发生在初始化列表中而不是函数体内，在函数体内进行的"初始化"实际上是赋值***
  - ***对于类类型,降低了程序效率，对于内置类型在初始化列表还是在函数体内初始化对于效率没有影响***
  - ***在某些特殊情况(例如const变量和引用)必须在初始化列表进行初始化***

## ***非局部变量的初始化顺序替换为函数运用结合***

1. ***既然被替换了，那么肯定被替换者有什么问题，那我们来好好瞧瞧***

   - *接下来场景为多文件场景，有两个文件，分别是 "init_before_user.cpp", "init_before_user1.cpp"*

   - ```cpp
     // init_before_user.cpp
     #include "foo.h"
     Foo globalFoo(123, "globalFoo")
       
     int main() {
       printf("===main===\n");
     }
     ```

     ```cpp
     // init_before_user1.cpp
     #include "foo.h"
     extern Foo globalFoo;
     class Bar {
     public:
       explicit Bar(Foo &foo) {
         printf("Bar(const Foo&)foo.a=%d\n", foo.a);
       }
     };
     
     Bar bar(globalFoo);
     ```

     *如果我们使用cmake*

     ```cpp
     add_executable(init_before_use
                   init_before_use.cpp
                   init_befor_user1.cpp
                   )
     ```

     ![](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303041407896.png)

     *就会发现Bar的foo.a的值是不对的，这是因为初始化文件的顺序是会影响初始化的顺序的*

     ```cpp
     add_executable(init_before_use
                    init_befor_user1.cpp
                   init_before_use.cpp
                   )
     ```

     *这样就正常了，但是这样我们肯定是不愿意看到的，坑非常大*

2. ***通过函数运用去解决***

   - ```cpp
     // init_before_user.cpp
     #include "foo.h"
     Foo& globalFoo() {
       static Foo globalFoo(123, "globalFoo");
       return globalFoo;
     }
       
     int main() {
       printf("===main===\n");
     }
     ```

     ```cpp
     // init_before_user1.cpp
     #include "foo.h"
     extern Foo& globalFoo();
     class Bar {
     public:
       explicit Bar(Foo& foo) {
         printf("Bar(const Foo&)foo.a=%d\n", foo.a);
       }
     };
     
     Bar bar(globalFoo);
     ```

     *这样解决就不存在之前说的初始化的问题*

