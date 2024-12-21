# 啥是RAII?


# 啥是RAII?

## ***什么是RAII***

1. *RAII是指C++语言中的一个惯用法（idiom），它是“Resource Acquisition Is Initialization”的首字母缩写。中文可将其翻译为“资源获取就是初始化”*
2. *虽然从某种程度上说这个名称并没有体现出该惯性法的本质精神，但是作为标准C++资源管理的关键技术，RAII早已在C++社群中深入人心*

## ***思路推导***

*如果你有写过C++或者RUST，你也许听过Resource Acquisition Is Initialisation (RAII), 但是并不了解这名字的含义是什么，或者不知道这个机制有什么用处。在这篇文章中，笔者将详细阐述RAII的原理以及它在资源管理方面巨大的威力*

*RAII是一种使用在面向对象语言中的资源（内存，互斥锁，或者文件描述符）管理机制，使用RAII的语言中，最出名的当属c++ and rust，正是因为强制RAII机制使得rust拥有了绝对的内存安全*

*Resource Acquisition Is Initialisation, 顾名思义，意味着任何资源的获取都应该发生在类的构造函数中，但我个人认为这个名字不太完备，有另一半的意思没有解释到，那就是资源的释放应发生在析构函数中，这意味着所有资源的 life cycle都与一个 object紧紧绑定在一起*

*我将用几段代码来具体阐述RAII的应用场景*

- *互斥锁*
  
  ```cpp
  std::mutex mut;
  
  int write_to_a_file_descriptor(std::string content)
  {
  
      mut.lock();
      // critical area below (might throw exception)
  
      // Writing  content to a file descriptor...
  
      // Critical areas above
      mut.unlock();
  }
  ```

*以上代码展示了一个将字符串写进某个文件描述符的函数，并且这个函数会被很多线程并行调用（这种情况在高并发线上服务的logger中非常常见），因此这个共用的文件描述符必须用一个互斥锁保护起来，否则不同线程的字符串会混在一起*

*这段代码看起来仿佛没有问题，但是如果当写IO时是抛出异常，call stack会被直接释放，也就意味着`unlock`不会执行，造成永久的死锁，这个问题可以像java一样用一个`try-catch`语句来避免但是也会让代码变得臃肿和难看。并且在复杂的逻辑中，往往很可能会忘了解锁，或者花很多精力来管理锁的获得和释放(如果在一个函数调用中有多处返回，每个return statement之前都需要 `unlock`)。这就是RAII发挥其威力的时候了，下面一段代码将展示如何用 `lock_guard`来使我们的代码异常安全并且整洁*

```cpp
std::mutex mut;

int write_to_a_file_descriptor(std::string content) 
{

    std::lock_guard<std::mutex> lock(mut);
    // critical area below (might throw exception)

    // Writing  content to a file descriptor...

    // Critical areas above
}
```

*`lock_guard`保证在函数返回之后释放互斥锁，因此使得开发人员不需要为抛出异常的情况担心且不需手动释放锁。但是 `lock_guard`是如何做到的呢？笔者将尝试自己手动实现一个 `lock_guard`*

```cpp
template <typename T>
class lock_guard
{
private:
    T _mutex;
public:
    explicit lock_guard(T &mutex) : _mutex(mutex) 
    {
        _mutex.lock();
    }
    ~lock_guard() 
    {
        _mutex.unlock();
    }
};
```

*从实现上可以看出，`lock_guard`在构造函数中锁住了引用传入的mutext，并且在析构函数中释放锁。其异常安全的保障就是析构函数一定会在对象归属的scope退出时自动被调用*

*如果你用过golang的话会知道golang的`defer`机制，这与C++的析构函数十分相似，但是golang的`defer`只能保证在函数返回前执行，而C++的析构函数可以保证在当前scope退出前执行（个人感觉golang的`defer`相比之下比较鸡肋）*

## ***智能指针***

*C++中一个非常常见的应用场景就是调用一个函数来产生一个对象，然后消费这个对象，最后手动释放指针。如以下代码所示*

```cpp
class my_struct
{
public:
    my_struct() = default;
};

template <typename T>
T* get_object()
{
    return new T();
}

int main()
{
    auto obj = get_object<my_struct>();
    // consume the object
    // ...
    // consume finish
    delete obj;
}
```

*然而，在大型应用程序中，指针的产生和消费错综复杂，写到后面程序员根本不记得自己有没有释放指针，或者某处地方读取一个已经释放的指针直接导致segmentation fault程序崩溃。而这就是C/C++各种内存泄漏的万恶之源*

*而自从C++11推出智能指针后，其极大地减轻了C++开发者们内存管理的压力。通过在裸指针上包一层智能指针，再也不用通过手动 `delete`来释放内存了。下面的代码将展示如何用 `std::unique_ptr`来管理指针*

```cpp
class my_struct
{
public:
    my_struct() = default;
};

template <typename T>
std::unique_ptr<T> get_object()
{
    return std::unique_ptr<T>(new T());
}

int main()
{
    auto obj = get_object<my_struct>();
    // consume the object
    // ...
    // consume finish
}
```

*智能指针的方便之处在于它会在自己的析构函数中执行 `delete`操作而不需程序员手动释放*

*在上述代码中，当main函数退出时， `std::unique_ptr`在自己的析构函数中释放指针，而为了防止有别的 `std::unique_ptr`指向自己管理的对象而导致的提早释放与空指针访问， `std::unique_ptr`禁止了 `copy constructor`与 `copy assignment`。有人可能会疑惑，为什么 `get_object`函数创建的 `unique_ptr`为什么没有在函数返回前释放指针？这是因为 `std::unique_ptr`实现了 `move constructor`（一种可以将资源从另一个对象“偷”过来的构造函数）并在返回时将指针传给了main函数中 `obj`变量*

*如果不太理解发生了什么，可以看一下以下我自己尝试实现的 `unique_ptr`.*

```cpp
template <typename T>
class unique_ptr
{
private:
    T* _ptr;
public:
    // Construct from plain pointer
    explicit unique_ptr(T* ptr) : _ptr(ptr) {
        std::cout << "unique_ptr constructed" << std::endl;
    };

    // Move constructor
    unique_ptr(unique_ptr &&ptr) noexcept : _ptr(ptr._ptr) {
        ptr._ptr = nullptr;
        std::cout << "unique_ptr move constructed" << std::endl;
    }

    // Copy constructor is forbidden
    unique_ptr(unique_ptr &ptr) = delete;

    // Move assignment
    unique_ptr& operator=(unique_ptr &&ptr) noexcept {
        if (this == &ptr) {
            return *this;
        }
        _ptr = ptr._ptr;
        ptr._ptr = nullptr;
        return *this;
    }

    // Copy assignment is forbidden
    unique_ptr& operator=(unique_ptr &ptr) = delete;

    ~unique_ptr() {
        delete _ptr;
        std::cout << "unique_ptr destructed" << std::endl;
    }

    T* operator->() {
        return _ptr; 
    }
};
```

*代码看上去比较复杂，不过我将一个方法一个方法地和大家分析*

1. *第8行代码实现了最基本的构造函数：从一个裸指针开始构造*
2. *第13行实现了 `move constructor`，这个方法会用一个已有的 `unique_ptr`来构造一个新的对象，它将旧 `unique_ptr`的指针替换为 `nullptr`来防止多个指针指向相同对象*
3. *第19行禁止了 `copy constructor`的使用，因为不允许多个指针指向同一对象*
4. *第22行实现了 `move assignment`，原理与 `move constructor`相同*
5. *第32行禁止了 `copy assignment`，原理与 `copy constructor`相同*
6. *第34行是析构函数，将最终释放指针*
7. *第39行实现了 `operatoroverload`，使得我们可以像访问普通指针一样访问 `unique_ptr`*

我们来用我们自己定义的 `unique_ptr`运行一下看会发生什么

```cpp
class my_struct
{
public:
    std::string _name = "name";

    my_struct() = default;
    explicit my_struct(std::string name) : _name(std::move(name)) {
        std::cout << "my_struct constructed" << std::endl;
    }
    ~my_struct() {
        std::cout << "my_struct destructed" << std::endl;
    }
};

template<typename T>
unique_ptr<T>get_object()
{
    return unique_ptr<T>(new T("struct name"));
}

int main() {
    unique_ptr<my_struct> obj = get_object<my_struct>();
    std::cout << obj->_name << std::endl;
}

// console output
my_struct constructed
unique_ptr constructed
struct name
my_struct destructed
unique_ptr destructed
```

*首先， `my_struct`被构造，然后 `unique_ptr`被构造，并且可以发现， `my_struct`的析构函数会在 `unique_ptr`的析构函数返回前执行，这意味着我们成功地将指针的life cycle绑定到了 `unique_ptr`上！不过，细心的同学可能发现了，全程 `unique_ptr`的 `move constructor`都没有被call过，但是我之前明确说了，main函数中的 `obj`是用 `get_object`函数中构造的 `unique_ptr`通过 `move constructor`构造的。可是为什么我们没有抓到 `move constructor`打印出来的东西呢？这是因为C++编译器做了一个叫做 `copy elision`的优化，来避免不必要的构造和析构，例如本例中，两个函数中的 `unique_ptr`对象其实是一个东西，因此他们之间的转换和赋值被优化掉了。如果我们通过 `std::move`来强制 `move constructor`发生，如下所示：*

*我们将看到这样的信息：*

```cpp
my_struct constructed
unique_ptr constructed
unique_ptr move constructed
unique_ptr destructed
struct name
my_struct destructed
unique_ptr destructed
```

*此时我们可以清晰地看到，main函数中的 `obj`是通过 `move constructor`构造的，并且在其构造完成之后， `get_object`函数中构造的 `unique_ptr`对象被析构了，因为我们已经提早将其内部指针替换成了 `nullptr`, 其析构函数什么都不会释放*

*智能指针中，除了 `std::unique_ptr`，还有其他类型，比如允许多个指针指向同一变量的 `std::shared_ptr`，其内存管理逻辑会复杂许多*

## ***技术总结***

***通过这篇文章，相信大家都体会到了RAII的威力，其将资源绑定到轻量级对象(比如智能指针，内存占用很少，可以像普通指针一样随意传递)的方法使得我们再也不需要关心在获取资源之后对资源的释放***

