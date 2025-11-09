# 组合模式


## ***数据结构模式***

1. ***什么是数据结构模式***
   - *常常有一些组件在内部具有特定的数据结构, 如果让客户程序依赖这些特定的数据结构, 将极大的破坏组件的复用*
2. ***典型模式***
   - *Composite*
   - *Iterator*
   - *Chain of Resposibility*

## ***动机***

1. *在软件某些情况下, 客户代码过多的依赖于对象容器复杂的内部实现结构, 对象容器内部实现结构(而非对象接口)的变化将引起客户代码的频繁变化, 带来了代码的维护性、扩展性等弊端*

2. *如何将"客户代码与复杂的对象容器结构"解藕?*

3. *让对象容器自己来实现自身的复杂结构, 从而使得客户代码就像处理简单对象一样来处理复杂的对象容器?*

## ***模式定义***

1. *将对象组合成树形结构以表示 "部分 - 整体" 的层次结构*
2. *Composite使得用户对单个对象和组合对象的使用具有一致性(稳定)*

## ***代码示例***

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

## ***要点总结***

1. *Composite模式采用树形结构来实现普遍存在的对象容器, 从而将"一对多"的关系转化为"一对一"的关系, 使得客户代码可以一致的(复用)处理对象和对象容器, 无需关心处理的是单个的对象, 还是组合的对象容器*
2. *将"客户代码与复杂的对象容器结构"解藕是Composite模式的核心思想，解藕之后, 客户代码将与纯粹的抽象接口----而非对象容器内部实现结构 发生依赖, 从而更能"应对变化"*
3. *Composite模式在具体实现中, 可以让父对象中的子对象反向追溯，如果父对象有频繁的遍历需求, 可使用缓存*

