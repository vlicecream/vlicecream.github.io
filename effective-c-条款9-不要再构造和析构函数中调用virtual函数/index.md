# 条款9-不要再构造和析构函数中调用virtual函数


## ***不要再构造和析构函数中调用virtual函数***

1. *如果在构造函数中调用了virtual函数，在实例化了派生类的时候，构造函数的执行顺序是从内到外，所以他会先调用父类的构造函数，但是父类的构造函数又调用了virtual函数，这时候派生类还没有初始化，所以调用的也只是父类的virtual函数*

   *如果在析构函数中调用了virtual函数，在销毁派生类的时候，析构函数的执行顺序是从外到内的，所以他会先销毁子类，再是父类，但是父类的析构函数又调用了virtual函数，这时候派生类已经被干掉了，所以调用的也只是父类的virtual函数*

   ```cpp
   class Base{
   public:
     Base() { sayHello(); }
     virtual void sayHello() {
       std::cout << "Hello Base!" << std::endl;
     }
     virtual void sayBye() {
       std::cout << "Bye Base!" << std::endl;
     }
     virtual ~Base() {
       sayBye();
     }
   }
   
   class Derived : public Base {
     Derived() { }
     void sayHello() override {
       std::cout << "Hello Derived" << std::endl;
     }
     void sayBye() override {
       std::cout << "Bye Derived" << std::endl;
     }
   }
   
   Derived derived; // 实例化派生类
   
   // 结果
   Hello Base!
   Bye Base!
   ```

   

