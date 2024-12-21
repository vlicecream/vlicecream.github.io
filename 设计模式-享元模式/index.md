# 享元模式


# 享元模式

## 对象性能模式

1. ***什么是对象性能***
   - *面向对象很好的解决了"抽象"的问题, 但是必不可免的要付出一定的代价，对于通常情况来讲, 面向对象的成本大都可以忽略不计，但是某些情况, 面向对象所带来的成本必须谨慎处理*
2. ***典型模式***
   - *Singleton*
   - *Flyweight*

## 享元模式动机

1. *在软件系统采用纯粹对象方案的问题在于大量细粒度的对象会很快充斥在系统中， 从而带来很高的运行时代价--主要指内存需求方面的代价.*
2. *如何在避免大量细粒度对象问题的同时, 让外部客户程序仍然能够透明的使用面向对象的方式来进行操作*

## 演示代码

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

## 模式定义

1. *运用共享技术有效的支持大量细粒度的对象*

## 要点总结

1. *面向对象很好的解决了抽象性的问题， 但是作为一个运行在机器中的程序实体, 我们需要考虑对象的代价问题，Flyweight主要解决面向对象的代价问题, 一般不触及面向对象的抽象性问题*
2. *Flyweight采用对象共享的做法来降低系统中对象的个数, 从而降低细粒度对象给系统带来的内存压力，在具体实现方面, 要注意对象状态的处理*
3. *对象的数量太大从而导致对象内存开销加大---什么样的数量才算大？这需要我们仔细的根据具体应用情况进行评估, 而不能凭空臆断*

