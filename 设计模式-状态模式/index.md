# 状态模式


# 状态模式

## 状态变化模式

1. ***什么是状态变化***
   - *在组件构建过程中, 某些对象的状态面临变化, 如何对这些变化进行有效的管理? 同时又保证高层的稳定?*
2. ***典型模式***
   - *Memento*
   - *State*

## 动机

1. *在软件构建过程中, 某些对象的状态如果改变, 其行为也会随之而发生变化*
   
   - *比如文档处于只读状态, 其支持的行为和读写状态支持的行为就可能完全不同*

2. *如何在运行时根据对象的状态来透明的更改对象的行为? 而不会为对象操作和状态转化之间引入紧耦合?*

## 模式定义

1. *允许一个对象在其内部状态改变时改变他的行为, 从而使对象看起来似乎修改了其行为*

## 代码示例

1. ```cpp
   /* Author: ting
    * Mail: vlicecream520@gmail.com 
    * Detail: (
    *   1. 状态模式示例
    *            一个程序员会随着一天的不同时间处于不同状态
    *
    *   2. 状态模式适用于 根据自己的状态发生变化 行为也随之发现变化
    *            他跟观察者模式不一样 观察者模式是 一个或多个对象 根据 一个对象的状态发生变化而发生变化
    * )
    * */
   
   #include <iostream>
   using namespace std;
   
   class Work;
   
   // 状态接口
   class State {
       public:
           // 与 work 行为相关的函数
           virtual void WriteProgram(Work *w) = 0;
   };
   ```

   // 工作类
   class Work {

       private:
           // 当前状态
           State* current;
           // 当前时间
           double hour;
           // 工作是否完成
           bool taskFinish;
    
       public:
           Work() { taskFinish = false; }
           // 设置/获取时间
           void SetTime(double hour) { this->hour = hour; }
           double GetTime() { return this->hour; }
           // 设置状态
           void SetState(State *s) { current = s; }
           // 设置工程是否完成
           void SetFinish() { this->taskFinish = true; }
           // 获取工作是否完成状态
           bool GetFinish() { return taskFinish; }
           // work 行为
           void WriteProgram() { current->WriteProgram(this); }

   };

   //下班休息状态
   class RestState : public State {
   public:
       void WriteProgram(Work *w) { cout << "当前时间:" << w->GetTime() << "点 工作完成，下班回家了" << endl; }
   };

   //睡眠工作状态
   class SleepingState : public State {
   public:
       void WriteProgram(Work *w) { cout << "受不了了," << w->GetTime() << "点了，先睡吧" << endl; }
   };

   //晚上工作状态
   class EveningState : public State {
   public:
       void WriteProgram(Work *w) {
           //任务完成了，可以休息了
           if(w->GetFinish()) {
               w->SetState(new RestState());
               w->WriteProgram();
           }
                   else {
               if(w->GetTime()<21) { cout << "当前时间:" << w->GetTime() << "点 加班了，疲惫至极" << endl; }
                           else {
                   //找过21点
                   w->SetState(new SleepingState());
                   w->WriteProgram();
                           }
           }
       }
   };

   //下午工作状态
   class AfternoonState : public State {
   public:
       void WriteProgram(Work *w) {
           if(w->GetTime()<17) { cout << "当前时间:" << w->GetTime() << "点 状态还不错，继续努力" << endl; }
                   else {
               w->SetState(new EveningState());
               w->WriteProgram();
           }
       }
   };

   //中午工作状态
   class NoonState : public State {
   public:
       void WriteProgram(Work *w)
       {
           if (w->GetTime()<13)
               cout<<"当前时间:"<<w->GetTime()<<"点 饿了，午饭:犯困，午休"<<endl;
           else
           {
               w->SetState(new AfternoonState());
               w->WriteProgram();
           }
       }
   };

   //上午工作状态
   class ForenoonState : public State {
   public:
       void WriteProgram(Work *w)
       {
           if(w->GetTime()<12)
               cout<<"当前时间:"<<w->GetTime()<<"点 上午工作，精神百倍"<<endl;
           else
           {
               w->SetState(new NoonState());
               w->WriteProgram();
           }
       }
   };

   void main()
   {
       //紧急项目
       Work *emergencyProjects = new Work();
       emergencyProjects->SetState(new ForenoonState());
       emergencyProjects->SetTime(9); 
       emergencyProjects->WriteProgram();

       emergencyProjects->SetTime(10);
       emergencyProjects->WriteProgram();
    
       emergencyProjects->SetTime(12);
       emergencyProjects->WriteProgram();
    
    
       emergencyProjects->SetTime(14);
       emergencyProjects->WriteProgram();
    
       //完成工作，不需要再加班了
       //emergencyProjects->SetFinish();
    
    
       emergencyProjects->SetTime(19);
       emergencyProjects->WriteProgram();
    
       emergencyProjects->SetTime(22);
       emergencyProjects->WriteProgram();

   }

```
## 要点总结

1. *State模式将所有与一个特定状态相关的行为都放入一个State的子类对象中, 在对象状态切换时, 切换相对应的对象，但同时维持State的接口, 这样实现了具体操作与状态转换之间的解藕*
2. *为不同的状态引入不同的对象使得状态转换变得更加明确, 而且可以保证不会出现状态不一致的情况, 因为转换是原子性的，即要么彻底转换过来 要么不转换*
3. *如果State对象没有实例变量, 那么各上下文可以共享一个State对象, 从而节省对象开销*
```

