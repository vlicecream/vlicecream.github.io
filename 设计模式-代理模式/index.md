# 代理模式


## ***接口隔离模式***

1. ***什么是接口隔离***
   - *在组件构建过程中, 某些接口之间直接的依赖 常常会带来很多问题 甚至根本无法实现*
   - *采用添加一层间接(稳定)接口, 来隔离本来互相紧密关联的接口是一种常见的解决方案*
2. ***典型模式***
   - *Façade*
   - *Proxy*
   - *Mediator*
   - *Adapter*

## ***动机***

1. *在面向对象系统中, 有些对象由于某种原因(比如对象创建的开销很大, 或者某些操作需要安全控制, 或者需要进程外的访问等)直接访问会给使用者或者系统结构带来很多麻烦*
2. *如何在不是去透明操作对象的同时来管理或者控制这些对象特有的复杂性？*
3. *增加一层间接层是软件开发中常见的解决方式*

## ***初始代码***

```cpp
class ISubject{
public:
    virtual void process();
};

class RealSubject: public ISubject{
public:
    virtual void process(){
        //....
    }
};

class ClientApp{
    ISubject* subject;
public:
    ClientApp(){
    	subject=new RealSubject();
	}
 
void DoTask(){
    //...
    subject->process();
 
    //....
}
};
```

## ***重构代码***

```cpp
class ISubject
{
public:
	virtual void process();
};

//Proxy的设计
class SubjectProxy: public ISubject
{
public:
    virtual void process(){
        //对RealSubject的一种间接访问
        //....
    }
};

class ClientApp
{
    ISubject* subject;
public:
    ClientApp()
    {
    	subject=new SubjectProxy();
	}

    void DoTask()
    {
        //...
        subject->process();

        //....
    }
};
```

## ***模式定义***

1. *为其他对象提供一种代理以控制(隔离，使用接口) 对这个对象的访问*

## ***要点总结***

1. *"增加一层间接层"是软件系统中对许多复杂问题的一种常见解决方法.*
2. *在面向对象系统中, 直接使用某些对象会带来很多问题, 作为间接层的proxy对象便是解决这一问题的常用手段*
3. *具体proxy设计模式的实现方法 实现粒度都相差很大*
4. *有些可能对单个对象做细粒度的控制, 如copy-on-write技术, 有些可能对组件模块提供抽象代理层, 在架构层次对对象做proxy*
5. *proxy并不一定要求保持接口完整的一致性, 只要能够实现间接控制, 有时候损失一些透明性是可以接受的*

