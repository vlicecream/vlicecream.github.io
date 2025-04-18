# 条款18~22 智能指针


# 条款18~22 智能指针

## ***前言***

1. *其实《Effective C++ Item13》已经过时了，所以我们可以在《Effective Modern c++ Item18~Item22》条款中去理解智能指针*
2. *我们不爱裸指针的原因*
   - *裸指针的声明没办法告诉我们它指向的是单个对象还是数组*
   - *没办法知道用完这个裸指针后要不要销毁它指向的对象*
   - *没办法知道怎么销毁这个裸指针，是用`operator delete`还是什么其它自定义的途径*
   - *参照原因1，没办法知道该用`delete`还是`delete[]`，如果用错了，结果未定义*
   - *很难保证调用路径上恰好销毁这个指针一次，可能内存泄露，也可能double free*
   - *通常没办法知道裸指针是否是空悬指针，即是否指向已销毁的对象*
3. *智能指针的作用*
   - *智能指针就是来解这些问题的，它们用起来像裸指针，但能避免以上的很多陷阱*
   - *C++11中有4种智能指针：`std::auto_ptr`、`std::unique_ptr`、`std::shared_ptr`、`std::weak_ptr`*
   - *其中`std::auto_ptr`已经过时了，C++11中可以被`std::unique_ptr`取代了*
4. *正式看本博客之前，需要去弄懂"PImpl"*
   - *具体关于PImpl是什么，可以查看我这个博客《[PImpl](https://vlicecream.github.io/%E7%BC%98%E8%B5%B7-pimpl/)》*
   - *还有如果前言都没好好看，导致Item22不知道PImpl是啥，这时候你是不是应该要质疑一下自己，学习是不是太浮躁了?*

## ***条款18 对于占有性资源使用`unique_ptr`***

1. *`std::unique_ptr`的特点*
   
   - *默认情况下，`std::unique_ptr`与裸指针一样大，且对于绝大多数操作来说（包括解引用），他们编译后的指令都是完全一样的，所有裸指针的空间和性能开销能满足要求的场景，`std::unique_ptr`一样能满足*
   
   - *`std::unique_ptr`体现了显式所有权的语义*
   
   - *非空的`std::unique_ptr`总是拥有他指向的对象*
   
   - *移动一个`std::unique_ptr`，所有权会从源指针转移到目的指针（之后源指针会设置为空指针）*
   
   - *拷贝`std::unique_ptr`是不允许的，因为如果你可以拷贝它，那么就有两个`std::unique_ptr`指向相同的资源，每一个都认为它拥有（和负责销毁）那份资源*
     
     - *因此`std::unique_ptr`是只可移动类型*
   
   - *当销毁的时候，一个非空的`std::unique_ptr`会销毁它的资源*
     
     - *默认情况下，资源销毁是通过对`std::unique_ptr`内的原生指针使用**delete**来完成的*
   
   - *一个例子是工厂函数。假设有一个基类和三个派生类，通过一个工厂函数来返回某个派生类的`std::unique_ptr`，这样调用方就不需要费心什么时候销毁返回的对象了：`std::unique_ptr`会负责这件事。*
     
     ```cpp
     class Investment {...};
     class Stock: public Investment {...};
     class Bond: public Investment {...};
     class RealEstate: public Investment {...};
     
     template <typename... Ts>
     std::unique_ptr<Investment> makeInvestment(Ts&&... params);
     
     auto pInvestment = makeInvestment(args);
     
     // 注意这里实际上有个所有权的转移：工厂函数通过std::unique_ptr将Investment对象的所有权转移给了调用者
     ```

2. *`std::unique_ptr`可以传入自定义的销毁器*
   
   - *在构造`std::unique_ptr`时，我们还可以传入一个自定义的销毁器，它会在`std::unique_ptr`析构时被调用，来销毁对应的资源*
   
   - *比如我们可能不想只是`delete obj`，还想输出一条日志*
     
     ```cpp
     auto delInvmt = [](Investment* pInvestment) {
         makeLogEntry(pInvestment); // make log
         delete pInvestment; // delete obj
     };
     
     template <typename... Ts>
     std::unique_ptr<Investment, decltype(delInvmt)> makeInvestment(Ts&&... params) {
         std::unique_ptr<Investment, decltype(delInvmt)> pInv(nullptr, delInvmt);
         if (...) {
             pInv.reset(new Stock(std::forward<Ts>(params)...));
         }
         ...
         return pInv;
     }
     ```
   
   - *从调用者的角度，你可以放心的处理`std::unique_ptr`，你可以相信在调用过程中资源只会销毁一次，且按恰当的方式销毁。理解以下几点能帮助你理解这种实现有多漂亮*
     
     - *`delInvmt`是自定义的销毁器，在`std::unique_ptr`析构时，自定义的销毁器会来完成释放资源必需的操作。这里用lambda表达式来实现`delInvmt`，不仅更方便，性能还更好*
     
     - *自定义的销毁器的类型必须与`std::unique_ptr`的第二个模板参数相同，因此我们要用`decltype(delInvmt)`来声明`std::unique_ptr`*
     
     - *`makeInvestment`的基本策略是创建一个空的`std::unique_ptr`，再令它指向合适的类型，再返回。其中我们把`delInvmt`作为第二个构造参数传给`std::unique_ptr`，从而将销毁器与`pInv`关联起来*
     
     - *无法将裸指针隐式转换为`std::unique_ptr`，需要用`reset`来修改`std::unique_ptr`持有的裸指针*
     
     - *我们在创建具体的对象时，使用了`std::forward`将`makeInvestment`的所有参数完美转发给对应的构造函数*
     
     - *注意`delInvmt`的参数是`Investment*`，而它的实际类型可能是派生类，因此需要基类`Investment`有一个虚的析构函数*
       
       ```cpp
       class Investment {
       public:
           ...
           virtual ~Investment();
           ...
       };
       ```
   
   - *前文我们说过在不提供自定义的销毁器时，`std::unique_ptr`的大小与裸指针相同。但在有了自定义的销毁器后，这个假设不成立了。销毁器的大小取决于它内部保存了多少状态。对于无状态的函数对象（例如捕获列表为空的lambda表达式），销毁器实际不占用任何空间，这就意味着当你需要一个无状态的销毁器时，在lambda表达式和函数间做选择，lambda表达式更好*
     
     ```cpp
     auto delInvmt1 = [](Investment* pInvestment) {
         ...
     };
     
     template <typename... Ts>
     std::unique_ptr<Investment, decltype(delInvmt1)> makeInvestment(Ts&&... args); // return type has size of Investment*
     
     void delInvmt2(Investment* pInvestment) {
         ...
     }
     
     template <typename... Ts>
     std::unique_ptr<Investment, void(*)(Investment*)> makeInvestment(Ts&&... args); // return type has size of Investment*
                                                                                     // plus at least size of function pointer
     ```
   
   - *`std::unique_ptr`另一个广泛应用的场景是pImpl模式*
   
   - *`std::unique_ptr`的两种形式分别是`std::unique_ptr<T>`和`std::unique_ptr<T[]>`，其中前者没有定义`operator[]`，后者在默认析构时会调用`delete[]`，且没有定义`operator*`和`operator->`。但在用到`std::unique_ptr<T[]>`的地方，你可能需要想一下是不是`std::vector`、`std::array`、`std::string`更合适。唯一一个用`std::unique_ptr<T[]>`更好的场合就是当你需要与C API交互时*
   
   - *`std::unique_ptr`另一个吸引人的地方在于，它可以作为`std::shared_ptr`的构造参数，因此上面的工厂函数返回`std::unique_ptr`就再正确不过了：调用者可以根据自己对所有权的需求来决定用`std::unique_ptr`还是`std::shared_ptr`，反正都支持*

3. *在C++11中，`std::unique_ptr`是表达独占所有权的方式，但它最吸引人的一个特性是它能即简单又高效地转化为`std::shared_ptr`*
   
   ```cpp
   std::shared_ptr<Investment> sp =    // 把 std::unique_ptr转换为
       makeInvestment(argument);         // std::shared_ptr
   ```
   
   *这是为什么`std::unique_ptr`如此适合做工厂函数的关键原因，工厂函数不会知道：独占所有权语义和共享所有权语义哪个更适合调用者。通过返回一个`std::unique_ptr`，工厂提供给调用者的是最高效的智能指针，但它不妨碍调用者用`std::shared_ptr`来替换它*

### ***Summary***

1. *`std::unique_ptr`是一个具有开销小，速度快，只可移动的智能指针，使用独占所有权语义管理资源*
2. *默认情况下，释放资源由`delete`来完成，也可以指定自定义的析构函数来替代，但是具有丰富状态的deleters和以函数指针作为deleters增大了`std::unique_ptr`的存储开销*
3. *很容易将一个`std::unique_ptr`转化为`std::shared_ptr`*

## ***条款19 需要共享所有权的资源管理，用`shared_ptr`***

1. *什么是`std::shared_ptr`*
   - *使用`std::shared_ptr`管理的对象的所有权是共享的，没有哪个`std::shared_ptr`特别拥有这个对象，而是最后一个`std::shared_ptr`析构时，销毁这个对象*
   - *与垃圾回收类似，调用者不需要手动管理`std::shared_ptr`管理的对象*
   - *与析构函数类似，对象的析构时间是确定的*
2. *`std::shared_ptr`的特点*
   - *`std::shared_ptr`内部有引用计数，被复制时，引用计数+1，有`std::shared_ptr`析构时，引用计数-1，当引用计数为0时，析构持有的对象*
3. *引用计数的存在有以下性能影响*
   - `*std::shared_ptr`的大小是裸指针的两倍：一个指针指向持有的对象，一个指针指向引用计数。*
   - *引用计数使用的内存必须动态分配，原因是`std::shared_ptr`的引用计数是非侵入式的，必须要独立在对象外面。用`std::make_shared`能避免这次单独的内存分配*
   - *引用计数的加减必须是原子的，因此你必须假设读写引用计数是有成本的*
   - *注意，不是所有`std::shared_ptr`的构造都会增加引用计数，移动构造就不会。因此移动构造一个`std::shared_ptr`要比复制一个更快*

*与`std::unique_ptr`类似，`std::shared_ptr`的默认销毁动作也是`delete`，且也可以接受自定义的销毁器*

*但与`std::unique_ptr`不同的是，`std::shared_ptr`的销毁器类型不必作为它的模板参数之一*

```cpp
auto loggingDel = [](Widget* pw) {
    makeLogEntry(pw);
    delete pw;
};

std::unique_ptr<Widget, decltype(loggingDel)> upw(new Widget, loggingDel);

std::shared_ptr<Widget> spw(new Widget, loggingDel);
```

*因此`std::shared_ptr`要比`std::unique_ptr`使用更灵活，比如不同销毁器的`std::shared_ptr`可以放到同一个容器中，而`std::unique_ptr`则不可以*

*另外，不同的销毁器不会改变`std::shared_ptr`的大小。`std::shared_ptr`内部需要为引用计数单独开辟一块内存，那么这块内存中再放一个销毁器也没什么额外开销。实际上这块内存被称为”控制块”，它里面包含以下元素*

- *引用计数*
- *弱引用计数*
- *其它数据，包括*
  - *自定义销毁器*
  - *内存分配器*
  - *等等*

*控制块的创建规则为*

- *`std::make_shared`总会创建一个控制块*
- *通过一个独享所有权的指针（如`std::unique_ptr`或`std::auto_ptr`）创建出的`std::shared_ptr`总会创建一个控制块*
- *通过裸指针创建的`std::shared_ptr`会创建控制块*

*一个推论就是：通过一个裸指针创建两个`std::shared_ptr`，会创建两个控制块，进而导致这个裸指针会被析构两次*

*从中我们可以得到两个教训*

- *不要直接用裸指针构造`std::shared_ptr`，尽量用`std::make_shared`。当然在需要自定义的销毁器时不能用`std::make_shared`*
- *非要用裸指针构造`std::shared_ptr`的话，尽量直接new，不要传入已有的裸指针变量*

*控制块会带来哪些开销呢？一个控制块通常只有几个word大，但其中会用到继承，甚至还有虚函数。这也意味着使用`std::shared_ptr`也会有调用虚函数的开销*

*但通常来说`std::shared_ptr`的额外开销是很小的。对于`std::make_shared`创建的`std::shared_ptr`，它的控制块只有3个word大，且内存分配上无额外成本。解引用一个`std::shared_ptr`也不会比解引用一个裸指针开销大。操作引用计数会带来一两次原子操作的开销，但通常也不大*

*`std::shared_ptr`的一个缺点是它不支持数组，但在C++11已经提供了`std::array`、`std::vector`、`std::string`这些容器类的前提下，还要用`std::shared_ptr`去管理一个数组，本身就是不好设计的信号*

### ***Summary***

1. *`std::shared_ptr`为了管理任意资源的共享式内存管理，提供了自动垃圾回收的便利*
2. *`std::shared_ptr`是原生指针的两倍大小，因为他们内部除了包含一个原生指针以外，还包含了一个引用计数*
3. *`std::shared_ptr` 是 `std::unique_ptr` 的两倍大，除了控制块，还有需要原子引用计数操作引起的开销*
4. *避免从原生指针类型变量创建 `std::shared_ptr`*
5. *引用计数的内存必须被动态分配，当然用`make_shared`来创建`shared_ptr`会避免动态内存的开销*
6. *引用计数的递增和递减必须是原子操作*
7. *资源的默认析构一般通过delete来进行，但是自定义的deleter也是支持的。deleter的类型对于 `std::shared_ptr` 的类型不会产生影响*

## ***条款20 在需要共享语义且可能空悬的地方用`weak_ptr`***

*有时候我们需要一种类似`std::shared_ptr`，但又不参与这个共享对象的所有权的智能指针。这样它就需要能知道共享对象是否已经销毁了。这就是`std::weak_ptr``*

*``std::weak_ptr`不是单独存在的，它不能解引用，也不能检测是否为空，它就是配合`std::shared_ptr`使用的*

*通常`std::weak_ptr`都是通过`std::shared_ptr`构造的，但它不会影响`std::shared_ptr`的引用计数*

```cpp
auto spw = std::make_shared<Widget>(); // ref count is 1
...
std::weap_ptr<Widget> wpw(spw);        // ref count remains 1
...
spw = nullptr;  
```

*可以用`expired()`来检测`std::weak_ptr`指向的对象是否有效*

```cpp
if (wpw.expired()) ...
```

*另一个常用的操作是`lock()`，它能原子地检测对象是否有效，以及返回这个对象的`std::shared_ptr`*

```cpp
std::shared_ptr<Widget> spw = wpw.lock(); // if wpw's expired, spw is null
```

*与之类似的操作是用`std::weak_ptr`构造一个`std::shared_ptr`*

```cpp
std::shared_ptr<Widget> spw(wpw);
```

*区别在于，如果`wpw`已经失效了，这次构造会抛`std::bad_weak_ptr`的异常*

*下面我们用几个例子来说明`std::weak_ptr`的必要性*

- *想象我们要实现一个cache，希望其中的元素在无人使用后被销毁。这里我们用`std::unique_ptr`并不合适，因为cache天然需要共享的语义。这样每个调用者都可以获得一个cache中元素的`std::shared_ptr`，它的生命期由调用者控制。cache内还需要保存一份元素的指针，且有能力检测它是不是失效了。这里我们需要的就是`std::weak_ptr`*
  
  ```cpp
  std::shared_ptr<const Widget> fastLoadWidget(WidgetID id) {
      static std::unordered_map<WidgetID, std::weak_ptr<const Widget>> cache;
      auto objPtr = cache[id].lock();
      if (!objPtr) {
          objPtr = loadWidget(id);
          cache[id] = objPtr;
      }
      return objPtr;
  }
  
  // 请不用在意上面的static，这只是个示意
  ```

- *第二个例子是设计模式中的“观察者模式”。它的一种典型实现是每个主题对象持有一组观察者的指针，每当主题对象有状态变化时依次通知每个观察者。这里主题对象不需要控制观察者的生命期，但需要知道观察者的指针是否还有效。用`std::weak_ptr`就可以非常自然的实现出这样的特性*

- *第三个例子是，当A和C都持有B的`std::shared_ptr`时，如果B也需要持有A的某种指针，该持有什么？*
  
  - *裸指针：如果A析构了，但C还在，B也就还在，此时B持有的A的裸指针就成了空悬指针，不好*
  - *`std::shared_ptr`：这样A与B就形成了循环依赖，永远不可能析构了*
  - *`std::weak_ptr`：唯一的好选择*

*但要注意的是，用`std::weak_ptr`来解`std::shared_ptr`可能造成的循环依赖，这种特性本身并没有价值。设计良好的数据结构，比如树，父节点控制子节点的生命期，但子节点也需要持有父节点的指针，这里最好的方案是父节点用`std::unique_ptr`来持有子节点，而子节点直接持有父节点的裸指针。即，严格层次结构，明确生命期的场景，不需要使用`std::weak_ptr`。`std::weak_ptr`的价值在于：在生命期不明确的场景，可以知道对象是否还有效*

*在效率方面，`std::weak_ptr`的大小与`std::shared_ptr`是相同的，它们使用相同的控制块，区别在于`std::weak_ptr`不会影响控制块中的引用计数，只会影响其中的弱引用计数*

### ***Summary***

1. *`std::weak_ptr` 用来模仿类似`std::shared_ptr`的可悬挂指针*
2. *潜在的使用 `std::weak_ptr`的场景包括缓存，观察者列表，以及阻止 `std::shared_ptr` 形成的环*

## ***条款21 优先用`make_unique`和`make_shared`而不是直接`new`***

1. *前言*
   
   - *先做一下介绍，`std::make_shared`是在C++11中增加的，但`std::make_unique`却是在C++14中增加的。如果你想在C++11中就用上`std::make_unique`，自己写一个简单版的也不难*
     
     ```cpp
     template <typename T, typename... Ts>
     std::unique_ptr<T> make_unique(Ts&&... params) {
         return std::unique_ptr<T>(new T(std::forward<Ts>(params)...));
     }
     ```
     
     *这个版本不支持数组，不支持自定义的销毁器，但这些都不重要，它足够用了。但要记住的是，不要把它放到`namespace std`下面*

2. *优先用这两个make的好处*
   
   - *这两个make函数的功能就不解释了，和它们类似的还有一个`std::allocate_shared`*
     
     ```cpp
     auto upw1(std::make_unique<Widget>());
     std::unique_ptr<Widget> upw2(new Widget);
     
     auto spw1(std::make_shared<Widget>());
     std::shared_ptr<Widget> spw2(new Widget);
     ```
     
      *上面这个例子说明了用make函数的第一个好处：不需要重复写一遍类型。所有程序员都知道：不要重复代码。代码越少，bug越少*
- *异常安全性。想象我们有两个函数*
  
  ```cpp
  void processWidget(std::shared_ptr<Widget> spw, int priority);
  int computePriority();
  ```
  
   *调用代码很可能长成这个样子*
  
  ```cpp
  processWidget(std::shared_ptr<Widget>(new Widget), computePriority()); // potential resource leak!
  ```
  
   *上面这行代码有内存泄漏的风险，为什么？根据C++标准，在`processWidget`的参数求值过程中，我们只能确定下面几点：*
  
  - *`new Widget`一定会执行，即一定会有一个`Widget`对象在堆上被创建。*
  
  - *`std::shared_ptr<Widget>`的构造函数一定会执行。*
  
  - *`computePriority`一定会执行。*
    
    *`new Widget`的结果是`std::shared_ptr<Widget>`构造函数的参数，因此前者一定早于后者执行。除此之外，编译器不保证其它操作的顺序，即有可能执行顺序为：*
  1. *`new Widget`*
  
  2. *执行`computePriority`*
  
  3. *构造`std::shared_ptr<Widget>`*
     
     *如果第2步抛异常，第1步创建的对象还没有被`std::shared_ptr<Widget>`管理，就会发生内存泄漏。*
     
     *如果这里我们用`std::make_shared`，就能保证`new Widget`和`std::shared_ptr<Widget>`是一起完成的，中间不会有其它操作插进来，即不会有不受智能指针保护的裸指针出现：*
     
     ```cpp
     processWidget(std::make_shared<Widget>(), computePriority()); // no potential resource leak
     ```
3. *更高效*
   
   - ```cpp
     std:shared_ptr<Widget> spw(new Widget);
     ```
     
     *这行代码中，我们以为只有一次内存分配，实际发生了两次，第二次是在分配`std::shared_ptr`控制块。如果用`std::make_shared`，它会把`Widget`对象和控制块合并为一次内存分配*
     
     *但是make函数也有一些缺点。*
     
     - 第一个缺点：无法传入自定义的销毁器。
     - 第二个缺点：make函数初始化时使用了括号初始化，而不是花括号初始化，比如`std::make_unique<std::vector<int>>(10, 20)`创建了一个有着20个值为10的元素的`vector`，而不是创建了`{10, 20}`这么两个元素的`vector`(参见[Item7](https://fuzhe1989.github.io/2017/05/22/effective-modern-cpp-chapter3-iterm7-10/))。
     - 第三个缺点：对象和控制块分配在一块内存上，减少了内存分配的次数，但也导致对象和控制块占用的内存也要一次回收掉。即，如果还有`std::weak_ptr`存在，控制块就要在，对象占用的内存也没办法回收。如果对象比较大，且`std::weak_ptr`在对象析构后还可能长期存在，那么这种开销是不可忽视的。
     
     如果我们因为前面这三个缺点而不能使用`std::make_shared`，那么我们要保证，智能指针的构造一定要单独一个语句。回到之前`processWidget`的例子中，假设我们有个自定义的销毁器`void cusDel(Widget* ptr);`，因此不能使用`std::make_shared`，那么我们要这么写来保证异常安全性：
     
     ```xpp
     std::shared_ptr<Widget> spw(new Widget, cusDel);
     processWidget(spw, computePriority());
     ```
     
     *但这么写还不够高效，这里我们明确知道`spw`就是给`processWidget`用的，那么可以使用`std::move`，将其转为右值，来避免对引用计数的修改：*
     
     ```cpp
     std::shared_ptr<Widget> spw(new Widget, cusDel);
     processWidget(std::move(spw), computePriority());
     ```

### ***Summary***

1. *和直接使用new相比，使用make函数减少了代码的重复量，提升了异常安全度，并且，对于`std::make_shared`以及`std::allocate_shared`来说，产生的代码更加简洁快速*
2. *也会存在使用make函数不合适的场景：包含指定自定义的deleter,以及传递大括号initializer的需要*
3. *对于`std::shared_ptr`来说，使用make函数的额外的不使用场景还包含*
   - *带有自定义内存管理的class*
   - *内存非常紧俏的系统，非常大的对象以及比对应的`std::shared_ptr`活的还要长的`std::weak_ptr`*

## ***条款22 在用到PImpl惯用法时，在实现文件中定义特殊成员函数***

*我们经常用名为PImpl的方法来实现接口与实现分离，进而大大降低程序构建的时间*

*PImpl是指把类A中的所有数据成员都移到一个impl类中，A中只留下一个impl类型的指针*

*举一个例子*

```cpp
class Widget {
public:
    Widget();
    ...
private:
    std::string name;
    std::vector<double> data;
    Gadget g1, g2, g3;        // Gadget is some user-defined type
};
```

*`Widget`的数据成员的类型为`std::string`、`std::vector<double>`、`Gadget`，这样就至少要include三个头文件，这也意味着每个需要include了这个包含`Widget`定义的头文件的地方，都被动引入了三个头文件。如果有一天我们修改了`Widget`的实现，比如增加或删除了一个成员，即使它们都是private的，即使接口完全没有变化，所有include它的用户文件都要重新编译。我们不想污染用户文件，也不想用户文件因为我们的实现修改而重新编译，我们就可以用PImpl*

```cpp
class Widget {
public:
    Widget();
    ~Widget();
    ...
private:
    struct Impl;
    Impl* pImpl;
};
```

*注意这里出现的`Impl`类型只是声明，没有定义，称为“不完整类型”，这样的类型只支持很少的操作，其中包括了我们需要的：声明一个不完整类型的指针*

*对应的实现文件内容为*

```cpp
struct Widget::Impl {
    std::string name;
    std::vector<double> data;
    Gadget g1, g2, g3;
};

Widget::Widget() : pImpl(new Impl) {}

Widget::~Widget()
{
    delete pImpl;
}
```

*有了智能指针后，我们觉得直接`new`和`delete`不好，需要用`std::unique_ptr`*

```cpp
class Widget {
public:
    Widget();
    ...
private:
    struct Impl
    std::unique_ptr<Impl> pImpl;
};
```

*因为不需要手动的`delete`，我们没有自己实现`Widget`的析构函数*

*看起来都很美好，编译也没问题，但在用户要用时，出事了*

```cpp
Widget w; // error!!!
```

*究其原因，是因为我们没有给`Widget`实现自定义的析构函数，因此编译器为`Widget`准备了一个。这个析构函数会被放到`Widget`的定义体内，默认是内联的，因此会有一份实现在用户文件中。`~Widget`中只做一件事：析构`pImpl`，即析构一个`std::unique_ptr<Impl>`。注意，我们隐藏了`Impl`的实现，在析构`std::unique_ptr<Impl>`时编译器发现`Impl`还是个不完整类型，此时对它调用`delete`是危险的，因此编译器用`static_cast`禁止了这种行为*

*解决方案很简单：自己实现`Widget`的析构函数*

```cpp
// widget.h
class Widget {
public:
    Widget();
    ~Widget();
    ...
private:
    struct Impl
    std::unique_ptr<Impl> pImpl;
};
// widget.cpp
...
Widget::Widget() : pImpl(std::make_unique<Impl>()) {}

Widget::~Widget() {}
```

*参考条款17，更好的方法是将析构函数定义为`= default`*

```cpp
Widget::~Widget() = default;
```

*根据条款17，自定义的析构函数会阻止编译器生成移动构造函数和移动赋值函数，因此如果你想要`Widget`有移动的能力，就要自己实现*

```cpp
class Widget {
public:
    Widget();
    ~Widget();
    Widget(Widget&& rhs) = default; // right idea, wrong code!
    Widget& operator=(Widget&& rhs) = default;
    ...
};
```

*注意不要在这些特殊成员函数的声明后面加`= default`，这样会重复上面析构函数的问题：会被内联，因此在用户代码中有一份实现，遇到不完整类型，game over。我们要做的就是在.cpp中将它们的实现定义为`= default`*

*接下来就是复制构造函数和复制赋值函数了。我们用`std::unique_ptr`是为了更好的实现PImpl方法，这也导致了`Widget`无法自动生成复制函数（`std::unique_ptr`不支持），但这并不意味着`Widget`就不能支持复制了，我们还可以自己定义两个复制函数*

```cpp
// widget.h
class Widget {
public:
    ...
    Widget(const Widget& rhs);
    Widget& operator=(const Widget& rhs);
    ...
};
// widget.cpp
Widget::Widget(const Widget& rhs)
    : pImpl(nullptr) {
    if (rhs.pImpl) {
        pImpl = std::make_unique<Impl>(*rhs.pImpl);
    }
}
Widget& Widget::operator=(const Widget& rhs) {
    if (!rhs.pImpl) {
        pImpl.reset();
    } else if (!pImpl) {
        pImpl = std::make_unique<Impl>(*rhs.pImpl);
    } else {
        *pImpl = *rhs.pImpl;
    }
}
```

*有意思的是，如果你把`pImpl`的类型改为`std::shared_ptr<Impl>`，你会发现上面所有这些注意事项，都不见了。你不需要手动实现析构函数、移动函数、构造函数，程序编译仍然是好的。*

*这种差异来自于`std::unique_ptr`和`std::shared_ptr`对自定义销毁器的支持方式不同。`std::unique_ptr`的目标是从体积到性能上尽可能与裸指针相同，因此它将销毁器类型作为模板参数的一部分，这样实现起来更高效，代价是各种特殊函数在编译时就要知道元素的完整类型。而`std::shared_ptr`没有这种性能上的要求，因此它的销毁器不是模板参数的一部分，性能会有一点点影响，但好处是不需要在编译特殊函数时知道元素的完整类型*

> `std::shared_ptr`在构造时就把销毁器保存在了控制块中，之后即使传递到了不知道元素完整类型的地方，它仍然能调用正确的销毁器来销毁元素指针。而`std::unique_ptr`是依靠模板参数提供的类型信息来进行销毁，因此必须要知道元素的完整类型

