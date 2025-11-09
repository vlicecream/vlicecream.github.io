# 模板方法


## ***组件协作模式***

1. ***什么是组件协作模式***
   - *现代软件专业分工之后的第一个结果就是 **框架与应用程序的划分***
   - ***组件协作*** *模式通过晚绑定，来实现框架和应用之间的松耦合，是二者之间协作时常用的模式*
2. **典型模式**
   - *Template Method*
   - *Observer / Event*
   - *Strategy*

## ***使用模板方法的动机***

1. *在软件构建的过程中，对于某一项任务，他尝尝有**稳定**的整体操作结构，但是各个子步骤却有很多**改变**的需求，或者由于固有原因（比如框架与应用之间的关系）而无法和任务的整体结构同时实现*
2. *那么如何在确定稳定操作结构的前提下，来灵活应对各个子步骤的变化或者晚期实现需求*

## ***早绑定代码***

1. ***什么是早绑定***
   
   - ![早绑定](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220525214958186.png)

2. ***早绑定代码演示***
   
   - *程序库开发人员 为了实现一个库 写了三个步骤*
     
     ```cpp
     class Library{
     public:
         void Step1(){
             //...
         }
     
         void Step3(){
             //...
         }
     
         void Step5(){
             //...
         }
     };
     ```
   
   - *app开发人员也要做步骤 并且还要写出一个步骤走的流程*
     
     ```cpp
     class Application{
     public:
         bool Step2(){
             //...
       }
     
         void Step4(){
             //...
       }
     };
     
     int main() {
         Library lib();
         Application app();
     
         lib.Step1();
     
         if (app.Step2()){
             lib.Step3();
         }
     
         for (int i = 0; i < 4; i++){
             app.Step4();
         }
     
         lib.Step5();
     
     }
     ```

## ***晚绑定代码***

1. ***什么是晚绑定***
   
   - ![晚绑定](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220525215014776.png)

2. ***晚绑定演示代码***
   
   - *程序库开发人员在开发的时候直接把主流程写好 调用每一步流程，把应用程序开发人员要开发的步骤定义成虚函数*
     
     ```cpp
     class Library{
     public:
       // 稳定 template method
       void Run() {
         Step1();
     
         if (Step2()) Step3; // 支持变化 --> 虚函数的多态调用
     
         for (int i = 0; i < 4; i++) Step4();  // 支持变化 --> 虚函数的多态调用
     
         Step5();
       }
       virtual ~Library() { }
     protected:
       void Step1() {
         ...
       }
       void Step3() {
         ...
       }
       void Step5() {
         ...
       }
       virtual bool Step2() = 0; //变化
       virtual void Step4() = 0; //变化
     };
     ```
   
   - ***应用程序开发人员只需要子类重写就即可***
     
     ```cpp
     class Application : public Library {
     protected:
       virrual bool Step2() {
         // 子类重写实现
       }
       virtual void Step4() {
         // 子类重写实现
       }
     };
     
     int main() {
       Library* pLib = new Application;
       lib->Run();
     
       delete pLib;
     }
     ```

## ***模式定义***

1. *定义一个操作中的算法骨架（稳定） -----> 也就是上面晚绑定代码，先把流程写出来*
2. *讲一些步骤延迟（变化）到子类中 -----> 也就是上面晚绑定代码，子类去实现 "2" "4" 两个方法*
3. *Template Method使得子类可以不改变(复用)一个算法的结构即可重定义(override 重写)该算法的某些特定步骤*

## ***再次强调的点***

1. ***如果一个骨架不稳定(代码中) 那么这个模版方法就不适用 如果夸张一点 都不稳定 那没有任何一种设计模式适合 设计模式就是需要一个稳定点***
2. ***如果反过来也是一样的道理，如果一个代码都是稳定的，那设计模式都不用存在的***
3. ***所以，别闲的胃疼了，这两种极端都是不存在的，但是从这可以看出来，我们一定要分出哪些是稳定的 那些是变化的***

## ***要点总结***

1. *Template Method模式是一种非常基础性的设计模式, 在面向对象系统中有着大量的应用，他用最简洁的机制（虚函数的多态），为很多应用程序框架提供了灵活的扩展点*
2. *Template Method模式除了可以灵活应对子步骤的变化外，他也实现了早绑定 -> 晚绑定的这一思路，也就是**不要调用我，让我来调用你**，反向控制结构是模版方法的典型应用*
3. *在具体实现方面，被模板方法调用的虚函数可以实现，也可以没有任何实现，可以把他们设置为protected方法*

