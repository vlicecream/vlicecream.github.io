# 原型模式


## ***对象创建模式***

1. ***什么是对象创建***
   - *通过对象创建模式，绕开new，来避免对象创建(new)过程中所导致的紧耦合(依赖具体类)从而支持对象创建*
   - ***他是接口抽象之后的第一步工作***
2. ***典型模式***
   - *Factory Method*
   - *Abstract Factory*
   - *Prototype*
   - *Builder*

## ***原型动机***

1. *在软件系统中，经常面临着"某些接口复杂的对象"的创建工作，由于需求的变化, 这些对象经常面临着剧烈的变化, 但是他们却拥有比较稳定一致的接口*
2. *如何应对这种变化? 如何向客户程序(使用这些对象的程序)隔离出 "这些易变对象", 从而使得"依赖这些易变对象的客户程序"不随着需求改变而改变*

## ***在工厂方法重构的基础上再次用原型模式重构的代码***

```cpp
class MainForm : public Form
{
    ISplitter*  prototype;//原型对象

public:

    MainForm(ISplitter*  prototype){
        this->prototype=prototype;
    }

    void Button1_Click(){

        ISplitter * splitter=
            prototype->clone(); //克隆原型

        splitter->split();
    }
};

//具体类
class BinarySplitter : public ISplitter{
public:
    virtual ISplitter* clone(){
        return new BinarySplitter(*this);
    }
};

class TxtSplitter: public ISplitter{
public:
    virtual ISplitter* clone(){
        return new TxtSplitter(*this);
    }
};

class PictureSplitter: public ISplitter{
public:
    virtual ISplitter* clone(){
        return new PictureSplitter(*this);
    }
};

class VideoSplitter: public ISplitter{
public:
    virtual ISplitter* clone(){
        return new VideoSplitter(*this);
    }
};

//抽象类
class ISplitter{
public:
    virtual void split()=0;
    virtual ISplitter* clone()=0; //通过克隆自己来创建对象

    virtual ~ISplitter(){}

};
```

## ***模式定义***

*使用原型实例指定创建对象的种类，然后通过拷贝这些原型来创建新的对象*

## ***类图***

![原型模式类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155825530.png)

## ***要点总结***

1. *prototype模式同样用语隔离类对象的使用者和具体类型(易变类)之间的耦合关系, 他同样要求这些"易变类"拥有"稳定的接口"*
2. *prototype模式对于"如何创建易变类的实体对象"采用"原型克隆"的方法来做。 他使得我们可以非常灵活的动态创建"拥有某些稳定接口"的新对象，所需工作仅仅是注册一个新类的对象(即原型) 然后在任何需要的地方clone*
3. *prototype模式中的clone方法可以利用某些框架中的序列化来实现深拷贝 c++里面一般都是拷贝构造*
4. *我们什么时候用factory method 或者 原型模式呢，简单来说 就是如果用几个步骤就用factory method模式，如果对象很复杂 有中间状态 你还想保留中间状态 就用 原型模式*

