# 条款12-复制对象时勿忘其每一个成分


## ***引言***

*在深拷贝和浅拷贝的理解中，我们知道了"拷贝构造函数"，并且也了解了他的构成*

*因此，在pass by value的应用场景中，我们可以写出以下的拷贝构造函数*

- ```cpp
  #include <iostream>
  #include<string>
  using namespace std;
  
  class A {
  public:
      A(int i) : count(i) {};
      A(const A& r) {                   // 拷贝构造函数
          count = r.count;
      }
      ~A() {};
      void out() {
          cout << "count:" << count << endl;
      }
  private:
      int count;
  };
  
  int main()
  {
      A a(1);
      A a1(a);              // 将a的临时变量传递进去
  
      a1.out();
      return 0;
  }
  ```

## ***不要忘记复制每一个成员***

*在引言的例子里，我们完整将count通过拷贝构造函数复制了过来。如果我们此时有了新需求，需要给类A增加一个成员x，那么在拷贝构造函数中，一定不要忘记拷贝x!*

- ```cpp
  #include <iostream>
  using namespace std;
  
  class A {
  public:
      A(int i, int j) : count(i),x(j) {};
      A(const A& r) {
          count = r.count;
          x = r.x;                                          // 不要忘记赋值成员x!
      }
      ~A() {};
      void out() {
          cout << "count:" << count << endl;
          cout << "x:" << x << endl;
      }
  private:
      int count;
      int x;
  };
  
  int main()
  {
      A a(1, 1);
      A a1(a);
  
      a1.out();
      return 0;
  }
  ```

  *值得注意的是：如果我们遗漏了第9行代码，编译器是不会报错的，甚至也能正常运行，但是不会得出想要的结果1*

## ***拥有"显示"拷贝构造函数发生继承***

*在上面的A类中， 我们已经针对A写出了显示的拷贝构造函数。但是如果此时发生了继承会怎么样呢？*

- ```cpp
  class B: public A {
  public:
      B(int x, int y) : b1(x),b2(y){};
      B(const B& r){
          b1 = r.b1;
          b2 = r.b2;
      };
      ~B() {};
      void out() {
          cout << "b1:" << b1 << endl;
          cout << "b2:" << b2 << endl;
      }
  private:
      int b1, b2;
  };
  ```

  *简直跟class A一样有木有*

  *于是我们开始编译 `B b(2,2); B b1(b);` ,会出现以下错误：`error C2512: 'CPS' : no appropriate default constructor available`*

  *这是为什么呢*

  *在类B的拷贝函数中看起来好像赋值了B中的每一个东西，但是它们复制的只是B声明的成员变量b1,b2;B继承A的成员变量附件完全木有得到复制啊*

  *在创建对象时，会首先调用A类的构造函数。而在B的初始化列表中，并没有显示的对基类的构造函数进行调用。那么，系统会默认调用A的无参构造函数，但是你的A类并没有定义无参构造函数，所以出错了*

  *改法如下*

  ```cpp
  class B: public A {
  public:
      B(int x, int y) : b1(x),b2(y), A(x, y) {};    // 调用A的构造函数
      B(const B& r) : A(r.b1, r.b2){                // 调用A的构造函数
          b1 = r.b1;
          b2 = r.b2;
      };
      ~B() {};
      void out() {
          cout << "b1:" << b1 << endl;
          cout << "b2:" << b2 << endl;
      }
  private:
      int b1, b2;
  };
  ```

## ***总结***

1. *拷贝函数应该确保复制对象内的所有成员变量及所有的基类成分*
2. *不要尝试以某个拷贝函数实现另一个拷贝函数。应该将共同机能放进第三个函数中，并由两个拷贝函数共同调用*

