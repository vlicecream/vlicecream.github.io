# 单例模式


## ***对象性能模式***

1. ***什么是对象性能***
   - *面向对象很好的解决了"抽象"的问题, 但是必不可免的要付出一定的代价，对于通常情况来讲, 面向对象的成本大都可以忽略不计，但是某些情况, 面向对象所带来的成本必须谨慎处理*
2. ***典型模式***
   - *Singleton*
   - *Flyweight*

## ***单例模式动机***

1. *在软件系统中 经常有这样一些特殊的类, 必须保证他们在系统中只存在一个实例, 才能确保他们的逻辑正确性以及良好的效率*
2. *如何绕过常规的构造器, 提供一种机制来保证一个类只有一个实例*
3. *ps：这个是类设计者的责任, 而不是使用者的责任*

## ***演示代码***

```cpp
class Singleton{
private:
    Singleton();
    Singleton(const Singleton& other);
public:
    static Singleton* getInstance();
    static Singleton* m_instance;
};

Singleton* Singleton::m_instance=nullptr;

//线程非安全版本
Singleton* Singleton::getInstance() {
    if (m_instance == nullptr) {
        m_instance = new Singleton();
    }
    return m_instance;
}

//线程安全版本，但锁的代价过高
Singleton* Singleton::getInstance() {
    Lock lock;
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

//C++ 11版本之后的跨平台实现 (volatile)
std::atomic<Singleton*> Singleton::m_instance;
std::mutex Singleton::m_mutex;

Singleton* Singleton::getInstance() {
    Singleton* tmp = m_instance.load(std::memory_order_relaxed);
    std::atomic_thread_fence(std::memory_order_acquire);//获取内存fence
    if (tmp == nullptr) {
        std::lock_guard<std::mutex> lock(m_mutex);
        tmp = m_instance.load(std::memory_order_relaxed);
        if (tmp == nullptr) {
            tmp = new Singleton;
            std::atomic_thread_fence(std::memory_order_release);//释放内存fence
            m_instance.store(tmp, std::memory_order_relaxed);
        }
    }
    return tmp;
}
```

## ***模式定义***

1. *保证一个类仅有一个实例, 并提供一个该实例的全局访问点*

## ***要点总结***

1. *Singleton模式中的实例构造器可以设置为protected以允许子类派生*
2. *Singleton模式一般不要支持拷贝构造函数和clone接口, 因为这有可能导致多个对象实例, 与Singleton模式的初衷违背*
3. *如何实现多线程环境下安全的Singleton？ 注意对双检查锁的正确实现*

