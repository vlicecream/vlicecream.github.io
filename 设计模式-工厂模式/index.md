# 工厂模式


# 工厂模式

## 对象创建模式

1. ***什么是对象创建***
   - *通过对象创建模式，绕开new，来避免对象创建(new)过程中所导致的紧耦合(依赖具体类)从而支持对象创建*
   - ***他是接口抽象之后的第一步工作***
2. ***典型模式***
   - *Factory Method*
   - *Abstract Factory*
   - *Prototype*
   - *Builder*

## 工厂模式动机

1. *在软件系统中 经常面临着创建对象的工作 由于需求的变化 需要创建的对象的具体类型经常变化*
2. *如何应对这种变化? 如何绕过常规的对象创建方法(new), 提供一种"封装机制"来避免客户程序和这种"具体对象创建工作"的紧耦合?*

## 初始代码

1. ```cpp
   class ISplitter{
   public:
       virtual void split()=0;
       virtual ~ISplitter(){}
   };
   
   class BinarySplitter : public ISplitter{
   
   };
   
   class TxtSplitter: public ISplitter{
   
   };
   
   class PictureSplitter: public ISplitter{
   
   };
   
   class VideoSplitter: public ISplitter{
   
   };
   
   class MainForm : public Form
   {
       TextBox* txtFilePath;
       TextBox* txtFileNumber;
       ProgressBar* progressBar;
   
   public:
       void Button1_Click(){
   
           ISplitter * splitter=
               new BinarySplitter();//依赖具体类
       splitter->split();
       }
   };
   ```

## 重构代码

1. ```cpp
   //具体类
   class BinarySplitter : public ISplitter{
   
   };
   
   class TxtSplitter: public ISplitter{
   
   };
   
   class PictureSplitter: public ISplitter{
   
   };
   
   class VideoSplitter: public ISplitter{
   
   };
   
   //具体工厂
   class BinarySplitterFactory: public SplitterFactory{
   public:
       virtual ISplitter* CreateSplitter(){
           return new BinarySplitter();
       }
   };
   
   class TxtSplitterFactory: public SplitterFactory{
   public:
       virtual ISplitter* CreateSplitter(){
           return new TxtSplitter();
       }
   };
   
   class PictureSplitterFactory: public SplitterFactory{
   public:
       virtual ISplitter* CreateSplitter(){
           return new PictureSplitter();
       }
   };
   
   class VideoSplitterFactory: public SplitterFactory{
   public:
       virtual ISplitter* CreateSplitter(){
           return new VideoSplitter();
       }
   };
   
   //抽象类
   class ISplitter{
   public:
       virtual void split()=0;
       virtual ~ISplitter(){}
   };
   ```

   //工厂基类
   class SplitterFactory{
   public:
       virtual ISplitter* CreateSplitter()=0;
       virtual ~SplitterFactory(){}
   };

   class MainForm : public Form
   {
       SplitterFactory*  factory;//工厂
   public:
       MainForm(SplitterFactory*  factory){
           this->factory=factory;
       }

       void Button1_Click(){
           ISplitter * splitter=
               factory->CreateSplitter(); //多态new
       splitter->split();
       }

   };

```
## 模式定义

1. *定义一个用于创建对象的接口，让子类决定实例化哪一个类*
2. *Factory Method使得一个类的实例化延迟**(目的: 解藕，手段: 虚函数)**到子类*

## 类图

![工厂模式类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155731948.png)

## 要点总结

1. *Factory Method模式用于隔离类对象的使用者和具体类型之间的耦合关系，面对一个经常变化的具体类型,紧耦合关系(new)会导致软件的脆弱*
2. *Factor Method模式通过面向对象的手法,将所要创建的具体对象工作延迟到子类,从而实现一种扩展(并非更改)的策略，较好的解决了这种紧耦合关系*
3. *Factor Method模式解决"单个对象"的需求变化. 缺点在于要求创建方法/参数相同*
```

