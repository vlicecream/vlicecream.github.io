# 工厂模式


## ***对象创建模式***

1. ***什么是对象创建***
   - *通过对象创建模式，绕开new，来避免对象创建(new)过程中所导致的紧耦合(依赖具体类)从而支持对象创建*
   - ***他是接口抽象之后的第一步工作***
2. ***典型模式***
   - *Factory Method*
   - *Abstract Factory*
   - *Prototype*
   - *Builder*

## ***工厂模式动机***

1. *在软件系统中 经常面临着创建对象的工作 由于需求的变化 需要创建的对象的具体类型经常变化*
2. *如何应对这种变化? 如何绕过常规的对象创建方法(new), 提供一种"封装机制"来避免客户程序和这种"具体对象创建工作"的紧耦合?*

## ***初始代码***

```cpp
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

## ***重构代码***

```cpp
#include <iostream>
#include <memory> // 引入智能指针

// 为了让代码能编译，定义一个空的 Form 类
class Form {};

// =================================================================
// 1. 抽象产品 (Abstract Product)
//    定义了工厂方法所创建的对象的接口。
// =================================================================
class ISplitter {
public:
    virtual void split() = 0;
    virtual ~ISplitter() { std::cout << "ISplitter destructor called." << std::endl; }
};

// =================================================================
// 2. 具体产品 (Concrete Products)
//    实现了 ISplitter 接口。
// =================================================================
class BinarySplitter : public ISplitter {
public:
    void split() override {
        std::cout << "Splitting a BINARY file..." << std::endl;
    }
};

class TxtSplitter : public ISplitter {
public:
    void split() override {
        std::cout << "Splitting a TXT file..." << std::endl;
    }
};

class PictureSplitter : public ISplitter {
public:
    void split() override {
        std::cout << "Splitting a PICTURE file..." << std::endl;
    }
};

class VideoSplitter : public ISplitter {
public:
    void split() override {
        std::cout << "Splitting a VIDEO file..." << std::endl;
    }
};

// =================================================================
// 3. 抽象工厂 (Abstract Factory / Creator)
//    声明了工厂方法，该方法返回一个 ISplitter 类型的对象。
// =================================================================
class SplitterFactory {
public:
    virtual ISplitter* CreateSplitter() = 0; // 这就是“工厂方法”
    virtual ~SplitterFactory() {}
};

// =================================================================
// 4. 具体工厂 (Concrete Factories / Concrete Creators)
//    重写工厂方法以返回一个具体产品的实例。
// =================================================================
class BinarySplitterFactory : public SplitterFactory {
public:
    ISplitter* CreateSplitter() override {
        return new BinarySplitter();
    }
};

class TxtSplitterFactory : public SplitterFactory {
public:
    ISplitter* CreateSplitter() override {
        return new TxtSplitter();
    }
};

class PictureSplitterFactory : public SplitterFactory {
public:
    ISplitter* CreateSplitter() override {
        return new PictureSplitter();
    }
};

class VideoSplitterFactory : public SplitterFactory {
public:
    ISplitter* CreateSplitter() override {
        return new VideoSplitter();
    }
};

// =================================================================
// 5. 客户端 (Client)
//    客户端代码仅依赖于抽象接口（ISplitter 和 SplitterFactory）。
// =================================================================
class MainForm : public Form {
private:
    SplitterFactory* factory; // 持有一个工厂的引用

public:
    // 通过构造函数注入具体的工厂实例
    MainForm(SplitterFactory* fact) : factory(fact) {}

    void Button1_Click() {
        if (!factory) {
            std::cout << "Error: Factory is not set!" << std::endl;
            return;
        }

        // 多态创建：具体创建哪个 Splitter 由 factory 的类型决定
        ISplitter* splitter = factory->CreateSplitter(); 
        
        splitter->split();

        // 重要：必须手动释放内存，否则会造成内存泄漏！
        delete splitter;
        splitter = nullptr;
    }
};

// =================================================================
// 示例用法
// =================================================================
void RunExample() {
    // 场景1：主窗口需要处理文本文件
    SplitterFactory* txtFactory = new TxtSplitterFactory();
    MainForm txtMainForm(txtFactory);
    std::cout << "--- Text Form Simulation ---" << std::endl;
    txtMainForm.Button1_Click();
    delete txtFactory; // 清理工厂

    std::cout << "\n----------------------------\n" << std::endl;

    // 场景2：主窗口需要处理视频文件
    SplitterFactory* videoFactory = new VideoSplitterFactory();
    MainForm videoMainForm(videoFactory);
    std::cout << "--- Video Form Simulation ---" << std::endl;
    videoMainForm.Button1_Click();
    delete videoFactory; // 清理工厂
}

int main() {
    RunExample();
    return 0;
}
```

## ***模式定义***

1. *定义一个用于创建对象的接口，让子类决定实例化哪一个类*
2. *Factory Method使得一个类的实例化延迟**(目的: 解藕，手段: 虚函数)**到子类*

## ***类图***

![工厂模式类图](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/imagesimage-20220529155731948.png)

## ***要点总结***

1. *Factory Method模式用于隔离类对象的使用者和具体类型之间的耦合关系，面对一个经常变化的具体类型,紧耦合关系(new)会导致软件的脆弱*
2. *Factor Method模式通过面向对象的手法,将所要创建的具体对象工作延迟到子类,从而实现一种扩展(并非更改)的策略，较好的解决了这种紧耦合关系*
3. *Factor Method模式解决"单个对象"的需求变化. 缺点在于要求创建方法/参数相同*

