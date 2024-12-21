# 示例c++代码


# c++ 示例代码

## 前言

```cpp
在被吐槽代码写的烂之后 看了很多源码 学习别人怎么写的 也专门去看了设计模式
在学艺术的我 还是特别热衷于极简艺术的我 自己之前写的代码也实在看不进去

现在设计模式都看完了 用golang 还有 c++ 综合一下每个模式的代码示例 但是因为只是示范设计模式 不会遵守c++的代码规范,甚至是伪代码
本篇是 c++

但是听我一句劝 虽然看代码模板也可以让你去应付一些场景 但是还是要真正去学设计模式
学设计模式 = 学面向对象
要理清 稳定与变化 设计模式有些模式也是应用于架构层次的 所以有些模式 并没有好的代码模板
而且设计模式 是人们智慧的精华 很值得我们去学习其中的思路精髓
不要被眼前的表象代码迷惑了

好的代码是一种艺术 也可以通过你的代码诉说你的生活极简品味

                                                                                                                                                                                    ---壬寅年七月初二
```

## 模板方法

```cpp
/*
 * 模版方法讲解
 * eg: 厨房做菜的流程
 * 流程: 先开火 再做具体的菜 随后关火
 * */

#include <iostream>
using namespace std;

class Cooker {

public:
    void CookProcess() {
        Fire();
        CookDishes();
        OutFire();
    };

    void Fire() {
        cout << "开火~" << endl;
    };

    // 具体做什么菜 交给子类去实现
    virtual void CookDishes() = 0;

    // 关火
    void OutFire() {
        cout << "关火~" << endl;
    }
};

class CookTomato : public Cooker {
    void CookDishes() {
        cout << "干西红柿" << endl;
    };
};

int main() {
    Cooker* cooker = new CookTomato();

    cooker->CookProcess();

    return 0;
}
```

## 简单工厂

```cpp
#include <iostream>
using namespace std;

// 具体类
class Product {
public:
    Product() {}
    virtual ~Product() {}

    virtual void Function() = 0;
};

class ProductA : public Product {
public:
    void Function() {
        cout << "ProductA" << endl;
    }
};

class ProductB : public Product {
public:
    void Function() {
        cout << "ProductB" << endl;
    }
};

// 具体工厂
class NewProductFactory {
public:
    virtual Product* CreateProduct() = 0;
    virtual ~NewProductFactory() {};
};

class NewProductAFactory : public NewProductFactory {
public:
    virtual Product* CreateProduct() {
        return new ProductA();
    };
};

class NewProductBFactory : public NewProductFactory {
public:
    virtual Product* CreateProduct() {
        return new ProductB;
    };
};

// 调用product class类
class MainFrom {
    NewProductFactory* factory; // 工厂

public:
    MainFrom(NewProductFactory* factory) {
        this->factory = factory;
    }

    void click() {
        Product* product = factory -> CreateProduct();  // 多态new
        product->Function();
    }
};


int main() {
    NewProductAFactory* pa = new NewProductAFactory();
    MainFrom* mf = new MainFrom(pa);
    mf->click();
}
```

## 抽象工厂

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 访问数据库有关基类
class IDBConnection {

};

class IDBCommand {

};

class IDataReader {

};

class IDBFactory {
public:
    virtual IDBConnection* CreateDBConnection() = 0;
  virtual IDBCommand* CreateDBCommand()=0;
  virtual IDataReader* CreateDataReader()=0;
};

//支持MYSQL
class SqlConnection: public IDBConnection{

};
class SqlCommand: public IDBCommand{

};
class SqlDataReader: public IDataReader{

};


class SqlDBFactory:public IDBFactory{
public:
    virtual IDBConnection* CreateDBConnection()=0;
    virtual IDBCommand* CreateDBCommand()=0;
    virtual IDataReader* CreateDataReader()=0;
};


//支持Oracle
class OracleConnection: public IDBConnection{

};

class OracleCommand: public IDBCommand{

};

class OracleDataReader: public IDataReader{

};

class OracleDBFactory : public IDBFactory {
public:
    virtual IDBConnection* CreateDBConnection() = 0;
    virtual IDBCommand* CreateDBCommand() = 0;
    virtual IDataReader* CreateDataReader() = 0;
};

class EmployeeDAO {
    IDBFactory* dbFactory;

public:
    vector<EmployeeDAO> GetEmployees() {
        // 以下是伪代码 因为如果每个实现出来还挺麻烦
        // 重要的思路就是 在你只需要用一个工厂 你就可以把这个工厂下的都 多态new出来 还不失关联性
    IDBConnection* connection = dbFactory->CreateDBConnection();
    connection->ConnectionString("...");

    IDBCommand* command = dbFactory->CreateDBCommand();
    command->CommandText("...");
    command->SetConnection(connection); //关联性

    IDBDataReader* reader = command->ExecuteReader(); //关联性
    while (reader->Read()){

    }
    }
};
```

## 策略模式

```cpp
/* 策略模式
 * eg: 计算不同国家的税
 * */

#include <iostream>
using namespace std;

// 税的基类
class TaxStrategy {
public:
    // 税的具体算法
    virtual double Calculate(const double& Money) = 0;
    virtual ~TaxStrategy() { };
};

// 中国税收
class CNTax : public TaxStrategy {
public:
    double Calculate(const double& Money) {
            return TaxBase * Money;
    };

private:
    double TaxBase = 2;
};

// 德国税收
class GermanyTax : public TaxStrategy {
public:
    double Calculate(const double& Money) {
            return TaxBase * Money + 10;
    }
private:
    double TaxBase = 1.5;
};

// 工厂
class NewTaxFactory {
public:
    virtual TaxStrategy* CreateTax() = 0;
    virtual ~NewTaxFactory() { };
};

class NewCNTaxFactory : public NewTaxFactory {
public:
    virtual TaxStrategy* CreateTax() {
        return new CNTax();
    }
};

class NewGermanyTaxFactory {
public:
    virtual TaxStrategy* CreateTax() {
        return new GermanyTax();
    }
};

// 计算税的类
class SalesOrder {

public:
    SalesOrder(NewTaxFactory* TaxFactory) {
        this -> strategy = TaxFactory->CreateTax(); 
    }

    ~SalesOrder() {
        delete this->strategy;
    }

    double CalculateTax() {
        double Money = 10000;

        double val = strategy->Calculate(Money);

        return val;
    }

private:
    TaxStrategy* strategy;
};

int main() {
    // 算中国税收
    NewCNTaxFactory* CNTax = new NewCNTaxFactory();
    SalesOrder* SO = new SalesOrder(CNTax);
    double val = SO->CalculateTax();
    cout << val << endl;
    return 0;
}
```

## 装饰模式

```cpp
/* 装饰模式
 * eg: 男人类 有叫功能 在叫完之后 需要戴帽子和穿鞋子
 * */

#include <iostream>
using namespace std;


class Man {
public:
    void Speak() {
        cout << "男人使用了 尖叫 技能" << endl;
    }

    virtual void Cloth() {};

    virtual ~Man() { }
};

// 装饰抽象类
class ManWithCloth : public Man {
public:
    // 构造函数
    ManWithCloth(Man* PMan) :PMan(PMan) {}

    // 流程
    void Cloth() {
        PMan->Speak();
        DressUp();
    }

    // 穿衣服的方法
    virtual void DressUp() {}

    // 重写析构函数
    virtual ~ManWithCloth() {
        if (PMan != nullptr) {
            delete PMan;
            PMan = nullptr;
        }
    } 

private:
    Man* PMan;
};

// 通过抽象类实现戴帽子
class ManWearHat : public ManWithCloth {
public:
    ManWearHat(Man* PMan) : ManWithCloth(PMan) {}
    virtual void DressUp() { cout << "男人带上了绿帽子 奇怪的属性增加了" << endl; }
};

// 通过抽象类实现穿鞋子
class ManWearShoes : public ManWithCloth {
public:
    ManWearShoes(Man* PMan) : ManWithCloth(PMan) {}
    virtual void DressUp() { cout << "男人穿上了小鞋 防御力up" << endl;}
};

// 工厂
class ManFactory {
public:
    virtual Man* CreateMan() { return new Man(); };
    virtual ~ManFactory() {};
};

// 抽象类工厂
class ManWithClothFactory {
public:
    virtual ManWithCloth* CreateManWithCloth(Man* PMan) = 0;
    virtual ~ManWithClothFactory() {};
};

class ManWearHatFactory : public ManWithClothFactory {
public:
    virtual ManWithCloth* CreateManWithCloth(Man* PMan) {
        return new ManWearHat(PMan);
    }     
};

class ManWearShoesFactory : public ManWithClothFactory {
public:
    virtual ManWithCloth* CreateManWithCloth(Man* PMan) {
        return new ManWearShoes(PMan);
    }    
};

int main() {
    ManFactory* manFactory = new ManFactory();
    Man* man = manFactory->CreateMan();

    ManWearHatFactory* manWearHatFactory = new ManWearHatFactory();
    ManWearHat a = manWearHatFactory->CreateManWithCloth(man);
    a.DressUp();


    return 0;
}
```

## 桥模式

```cpp
/*
 * 场景:
 *    假如你有一个几何形状(shape)类, 可以扩展出两个子类(圆形, 方形)
 *    但是你又想给每一个几何都上颜色 比如现有(red, blue)
 *    但是，由于你已有两个子类，所以总共需要创建四个类才能覆盖所有组合，例如蓝色圆形(BlueCircle)和红色方形(RedSquare)
 *
 * 坏处:
 *    在层次结构中和颜色将导致代码复杂程序指数增长
 *    例如添加三角状, 你需要增加两个子类,也就是每种颜色一个
 *    此后新增一种新颜色就需要新增三个子类
 *    满满的子类数目将会急剧膨胀
 *
 * 可以利用桥模式去改善这个坏处 并封装变化
 */

#include <iostream>
using namespace std;


class Color {
public:
    virtual void Draw() = 0;
};

// 红色
class Red : public Color {
public:
    void Draw() { cout << "红色" << endl; }
};

// 蓝色
class Blue : public Color {
    public:
        void Draw() { cout << "蓝色" << endl; }
};

class Shape {

public:
    Shape(Color* c): color(c) {};
    virtual void Self() = 0;


protected:
    Color* color;
};

// 圆形
class Cyclo : public Shape {
public:
    Cyclo(Color* color) : Shape(color) {};
    void Self() { 
        cout << "我是圆形" << endl;
        color->Draw();
    }
};

// 方形
class Square : public Shape {
public:
    void Self() {
        cout << "我是方形" << color << endl; 
        color->Draw();
    }    
};
```

```cpp
int main() {
    Red* red = new Red();
    Cyclo* c = new Cyclo(red);    
    c->Self();
    return 0;
}
```

## 原型模式

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

## 单例模式

```cpp
/*
 * 单例模式
 * "单例模式" 是 "性能优化" 的作用 全程下只存在一个实例
 * 在内部会提供一个供外接访问的接口
 * 他是将其构造函数和拷贝构造函数设为私有
 * 它分为饿汉模式和懒汉模式
 * 懒汉模式 由于用到实例才会实例化 所以需要注意线程安全
 * 饿汉模式 由于一开始直接实例化 不需要注意线程安全 但是要注意返回一个指针实例
 * */

//*********************************懒汉*********************************
// 简单实现
class Singleton {
private:
    Singleton();
    Singleton(const Singleton& other);
public:
    static Singleton* getInstance();
    static Singleton* m_instance;
};

Singleton* Singleton::m_instance = nullptr;

//线程非安全版本
Singleton* Singleton::getInstance() {
    if (m_instance == nullptr) {
        m_instance = new Singleton();
    }
    return m_instance;
}

//双检查锁，但由于内存读写reorder不安全
Singleton* Singleton::getInstance() {

    if(m_instance==nullptr){
        Lock lock;
        if (m_instance == nullptr) {
            m_instance = new Singleton();
        }
    }
    return m_instance;
}

//*******************************************饿汉*********************************
class Singleton {
private:
   Singleton() {}
   static Singleton instance;    //静态变量只会有一份数据存在 从而保证只有一个实例
public:
   static Singleton* GetInstance() { return &instance; }                                                  
}
```

## 享元模式

```cpp
/* FileName: flyweight.h
 * Author: ting
 * Detail: {
 *   1. 享元模式示例
 *   2. flyweight模式 跟 单例模式 的作用是一样的 都是性能优化 为了减少对象的实例个数
 * }
 * */


#include <iostream>
#include<string>
#include<map>
using namespace std;

//用户类 用户网站的客户账号，是"网站"类的外部状态
class User
{
private:
    string m_name;
public:
    User(string name)
    {
        m_name = name;
    }
    std::string GetName()
    {
        return m_name;
    }
};

//抽象网站类 定义对象的内部状态和外部状态及其对应的方法
class WebSite
{
public:
    virtual ~WebSite() = default;
    virtual void Use(User user) = 0;
};

//此处为具体网站类  实现抽象享元角色的方法，在具体的角色中，实现具体方法时需要注意将内部状态与外部状态区分开，不应出现二者同时被修改的方法。
class ConcreteWebSite :public WebSite
{
private:
    string m_name;
public:
    ConcreteWebSite(std::string name)
    {
        m_name = name;
    }
    void Use(User user)override
    {
        cout << "网站分类：" << m_name << "  用户：" + user.GetName() << endl;
    }
};

//此处为网站工程类 负责创建和管理享元角色。当客户对象请求一个享元对象时，享元工厂检査系统中是否存在符合要求的享元对象，
//如果存在则提供给客户；如果不存在的话，则创建一个新的享元对象。
class WebSiteFactory
{
private:
    std::map<std::string, WebSite*> flyweights;
public:
    ~WebSiteFactory()
    {
        for (auto it = flyweights.begin(); it != flyweights.end(); ++it)
            delete it->second;
    }
    WebSite* GetWebSiteCategory(string key)
    {
        for (auto it = flyweights.begin(); it != flyweights.end(); ++it)
        {
            if (it->first == key)
                return it->second;
        }

        WebSite* website = new ConcreteWebSite(key);
        flyweights.insert(pair<std::string, WebSite*>(key, website));
        return website;
    }
    int GetWebSiteCount()
    {
        return flyweights.size();
    }
};

int main()
{
    WebSiteFactory f;

    WebSite* fx = f.GetWebSiteCategory("产品展示");
    fx->Use(User("小菜"));

    WebSite* fy = f.GetWebSiteCategory("产品展示");
    fy->Use(User("大鸟"));

    WebSite* fz = f.GetWebSiteCategory("产品展示");
    fz->Use(User("娇娇"));

    WebSite* fl = f.GetWebSiteCategory("博客");
    fl->Use(User("老顽童"));

    WebSite* fm = f.GetWebSiteCategory("博客");
    fm->Use(User("桃谷六仙"));

    WebSite* fn = f.GetWebSiteCategory("博客");
    fn->Use(User("南海鳄神"));

    cout << "得到网站分类总数：" << f.GetWebSiteCount() << endl;

    system("pause");
    return 0;
}
```

## 状态模式

```cpp
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

## 组合模式

```cpp
/* Author: ting
 * Date: 2022-08-14
 * Detail: 组合模式示例
 *     将多个对象依据树形结构看为一个整体 实现 "部分 - 整体"的效果
 *   1. 抽象示例(Component)角色: 他的主要作用是为了树叶构件声明公共接口, 并实现他们的默认行为
 *   2. 树叶构件(Composite)角色: 是组合中的分支节点对象, 他有子结点, 用于继承和实现抽象构件
 *            主要作用是存储和管理子部件 通常包含 Add() Remove() GetChild() 等方法
 *     3. 树叶构件(Leaf)角色: 是组合中的叶节点对象, 他没有子节点, 用于继承和实现抽象构件
 * */


// 计数
class Counter {
    public:
        virtual ~Counter() {};
        virtual int Count() = 0;
};

// 树叶构件
class City : public Counter {
public:
    City(int sum) : sum(sum) { };
    int Count() override { return sum; }

private:
    int sum;
};

// 树枝构件
class Composite : public Counter {
public:
    ~Composite() {
        for (auto& counter : counters) {
            delete counter;
        }
    }

    void add(Counter* counter) {
        counters.push_back(counter);
    }

    void del(Counter* counter) {
        auto it = find(counters.begin(), counters.end(), counter);
        counters.erase(it);
    }   

    int count() {
        int sum = 0;
        for (const auto& counter : counters) {
            sum += counter->count();
        }

        return sum;
    }

private:
    vector<Counter*> counters;
};

// 使用：统计人口数量
int main() {
    Composite* china = new Composite();
    City* beijing = new City(100000); // 直辖市
    City* shanghai = new City(50000); // 直辖市

    china->add(beijing);
    china->add(shanghai);

    Composite* shanxi = new Composite();
    shanxi->add(new City(3000)); // 大同市
    shanxi->add(new City(2000)); // 太原市

    china->add(shanxi);

    cout << china->count() << endl;
}
```

