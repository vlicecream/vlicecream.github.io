# 条款32~40 继承与面向对象设计


# 条款32~40 继承与面向对象设计

## ***前言***

1. *这一大章条款 主要是说的继承与面向对象设计，说实话我觉得没有看这一大章的必要，去看本博客的《[设计模式](https://vlicecream.github.io/categories/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/)》章节*
2. *设计模式这个名字太高大上，其实他就是可复用的面向对象设计，没错，你在学设计模式其实就是相当于学面向对象*
3. *设计模式也是基于面向对象设计原则的，所以你不懂设计模式，不懂面向对象设计原则，我觉得你看完这一章作用也不大*
4. *懂设计模式的希望你看本章节会有一些新的体会，其实每个条款都是对应着面向对象设计原则，同时也有很多设计模式可以解决这些条款的问题*
5. *没有错，在本大章节，我就是设计模式吹!!!*

## ***条款32 确保public继承是is-a关系***

1. *"is-a"的概念*
   
   - *以 C++进行面向对象编程，最重要的一个规则是：**public inheritance（公开继承）意味 "is-a"（是一种）的关系***
   
   - *如果你令 class D以 public 形式继承 class B，你便是告诉编译器：*
     
     - *每一个类型为D的对象同时也是一个类型为B的对象，反之不是*
     - *B对象可使用的地方，D对象一样可以使用，反之不是*
   
   - *下面的Student类 public 继承 Person类*
     
     ```cpp
     class Person {};
     class Student :public Person {};
     ```
     
     *任何获得类型为Person（pointer-to-Person或reference-to-Person）的实参，都可以接受一个Student（pointer-to-Student或reference-to-Student）对象*
     
     ```cpp
     void eat(const Person& p);
     void study(const Student& s);
     
     int main()
     {
         Person p;
         Student s;
     
         eat(p);    //正确
         eat(s);    //正确
         study(s);  //正确
         study(p);  //错误
     
         return 0;
     }
     ```
   
   - *上面的规则只对"public"继承才成立哦～，"private""protected"不成立*

2. *设计正确的继承模型*
   
   - *鸟可以飞，企鹅也是一种鸟。于是我们可能设计下面错误的继承模型*
     
     - *企鹅虽然属于鸟类，但是企鹅不会飞*
     - *设计中，我们错误的将鸟类中的`fly()`虚函数派生给了Penguin类*
     
     ```cpp
     //鸟类
     class Bird {
     public:
         virtual void fly();
     };
     
     //企鹅，也继承了fly()虚函数
     class Penguin : public Bird {};
     ```
   
   - *我们应该修改上面的代码，下面才是合适的模型，学过设计模式知道抽象思想的，其实就是基于抽象类再抽象了一层*
     
     ```cpp
     //鸟类
     class Bird {
         //无fly()函数
     };
     
     //会飞的鸟类
     class FlyingBird :public Bird {
     public:
         virtual void fly();
     };
     
     //企鹅不会飞
     class Penguin :public Bird {
     
     };
     ```

3. *以“编译期”确认关系代替“运行期”确认关系*
   
   - *还是基于上述 鸟和企鹅的例子*
     
     *企鹅不会飞，但是我们仍然让Bird定义`fly()`函数，然后让Penguin继承于Bird，与上面不同的是，我们让Penguin在执行`fly()`函数的时候报出一个错误（运行期执行）*
     
     ```cpp
     class Bird {
     public:
         virtual void fly();
     };
     
     void error(const std::string& msg);
     class Penguin :public Bird {
     public:
         virtual void fly() {
             error("Attempt to make a penguin fly!");
         }
     };
     ```
     
     *上面的代码是在运行期检查这种错误的，下面我们设计让编译器在编译的时候检查出企鹅不会飞这种错误*
     
     ```cpp
     class Bird {
         //无fly()函数
     };
     ```

     class Penguin :public Bird {
         //...
     };
    
     Penguin p;
     p.fly();
     ```
    
     *这个问题的关键是：并不是所有的鸟都会飞，因此Bird不应该暴露Fly接口*
    
     *所以还是再抽象一个会飞的鸟类接口，我觉得是可以的，但是也随之暴露一个问题了，面向对象设计原则有一个**类应该是单一职责**，如果不是单一职责，那么子类的数目就会急剧膨胀了*
    
     *其实知道装饰模式和桥模式，这里就可以利用 **组合**去优化，但是那是设计模式的知识点了，大家可以看《[装饰模式](https://vlicecream.github.io/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E8%A3%85%E9%A5%B0%E6%A8%A1%E5%BC%8F/)》《[桥模式](https://vlicecream.github.io/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E6%A1%A5%E6%A8%A1%E5%BC%8F/)》*

- *再考虑矩形和正方形，从几何角度讲，正方形是一种矩形。从软件设计角度讲，正方形是矩形吗？应该使用public继承吗？*
  
  *思考：对矩形可以单独设置宽度，而不影响高度。但是对于正方形，设置宽度，要求高度随之变化，否者就不是正方形了。因此不能使用public继承*

### ***Summary***

1. *public继承意味is-a*
   - *适用于base classes身上的每一件事情一定也适用于derived classes身上，因为每一个derived class对象也都是一个bass class 对象*
2. *在类的设计上 其实要蛮下一番心思的（这其实就是题外话了，推荐本博客《[设计模式](https://vlicecream.github.io/categories/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/)》专题）*

## ***条款33 避免遮掩继承而来的名称***

1. *C++基类和派生类的作用域为嵌套关系,同时存在作用域屏蔽规则,例如:*
   
   ```cpp
   class Base{
   public:
       void fun();
       ...
   private:
       int a;
       ...
   }
   class Derived:public Base{
       ...
   }
   ```
   
   *那么Derived和Base之间的作用域关系就像这样*
   
   ![img](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202303111858185.png)
   
   *如果Derived中没有定义a和fun,那么对在Derived作用域内对a的fun的使用将会由内而外直至全局作用域逐层查找;*
   
   *如果Derived中定义了a和fun,那么会使用Derived中的a和fun,但是如果Derived中a和fun的定义如果像这样:*
   
   ```cpp
   class Derived:public Base{
   public:
       void fun(int);
       string a;
       ...
   private:
       ...
   }
   ```
   
   *此时如果在Derived内存在如下语句:*
   
   ```cpp
   a=1;
   fun();
   ```
   
   *都会编译不通过,因为由于名字屏蔽,Base的a和fun在Derived中将不可见,这就是作用域屏蔽规则.因此派生类对基类函数的重写将不是overload & override,而是隐藏*

2. *在采用public继承时,如果派生类重写基类函数,名字屏蔽会使得基类中同名函数在派生类中不可见*
   
   *如果使基类的同名函数在派生类中仍然可见,可以使用using声明式*
   
   ```cpp
   class Derived:public Base{
   public:
       using Base::fun;
       void fun(int);
       ...
   private:
       string a;
       ...
   }
   ```
   
   *如果并不想继承Base类所有的fun函数(private继承中可能出现),则可以使用"转交函数"(forwarding function)的方法*
   
   ```cpp
   class Derived:private Base{
   public:
       void fun(){
           Base::fun();
       }
       void fun(int);
       ...
   private:
       string a;
       ...
   }
   ```

3. *此条款主要讲的也是违反了面向对象原则之一（里氏替换原则-我们应该去重写，而不是隐藏）*

### ***Summary***

1. *我们要小心谨慎 不能隐藏了父类函数*
2. *可以使用类名作用域决定调用父类还是子类的函数*

## ***条款34 接口继承与实现继承***

*说实话 这个思想我是真觉得你应该去看设计模式，光看这一个条款你可能不理解接口继承与实现继承，说到底其实就是一个抽象接口的思想，但是实现起来可是有一番难度的*

*本条款其实就是介绍了三种虚函数的好坏而已，同时看到这里大家有没有想起来《条款31》呀~*

*好，进入主题*

1. *继承中接口的处理方式*
   
   - 作为类的设计者，对于基类的成员函数可以大致做下面三种方式的处理：
     
     ​    ① 纯虚函数：基类定义一个纯虚函数，然后让派生类去实现
     
     ​    ② 非纯虚的virtual虚函数：基类定义一个非纯虚的virtual虚函数，然后让派生类去重写覆盖(override)
     
     ​    ③ 普通的成员函数：基类定义一个普通的成员函数，并且不希望派生类去隐藏
   
   - 本文依次介绍上面这三种设计的原理。下面定义一个类，作为本文讲解的基础：
     
     ```cpp
     - class Shape {
       public:
           virtual void draw()const = 0; //纯虚函数
           virtual void error(const std::string& msg); //非纯虚函数
           int objectID()const; //普通成员函数
       };
     
     class Rectangle :public Shape {};
     
     class Ellipse :public Shape {};
     ```

2. *纯虚函数*
   
   - *这是文章开始提到的第一种情况：派生类只继承基类的成员函数的接口（纯虚函数），派生类自己实现纯虚函数*
   
   - *纯虚函数的一些特征：*
     
     *① 拥有纯虚函数的类不能实例化*
     *② 拥有纯虚函数的类，其派生类必须实现该纯虚函数*
     
     ```cpp
     class Shape {
     public:
         virtual void draw()const = 0; //纯虚函数
     };
     
     class Rectangle :public Shape {};
     class Ellipse :public Shape {};
     ```
   
   - *其中涉及纯虚函数的目的为：*
     
     - *Shape是所有图形类的基类，其提供一个`draw()`的画图函数，但是由于其派生类（矩形、圆等）的画图方式都是不一样的，因此无法为`draw()`函数提供一种默认缺省行为，因此Shape将`draw()`定义为纯虚函数， 让其派生类去自动实现*
     
     ```cpp
     class Shape {
     public:
         virtual void draw()const = 0;
     };
     
     class Rectangle :public Shape {
     public:
         virtual void draw()const {
             std::cout << "Rectangle" << std::endl;
         }
     };
     class Ellipse :public Shape {
     public:
         virtual void draw()const {
             std::cout << "Ellipse" << std::endl;
         }
     };
     
     int main()
     {
         //Shape *ps = new Shape;    //错误，不能实例化
         Shape *ps1 = new Rectangle;
         Shape *ps2 = new Ellipse;
     
         ps1->draw(); //调用Rectangle::draw()
         ps2->draw(); //调用Ellipse::draw()
     
         return 0;
     }
     ```

3. *非纯虚的virtual虚函数*
   
   - *先来看一个virtual函数的演示案例*
     
     *假设某航天公司设计一个飞机继承体系，该公司现在只有A型和B型两种飞机，代码如下*
     
     ```cpp
     class Airport {}; //机场
     
     class Airplane {  //飞机的基类
     public:
         virtual void fly(const Airport& destination) {
             //飞机飞往指定的目的地(默认行为)
         }
     };
     
     //A、B两个派生类
     class ModelA :public Airplane {};
     class ModelB :public Airplane {};  
     // ModelB 哈哈哈 让我想起最近的一个新车啥车型 主持人说 - "ma de b" 笑死
     ```
     
     *`fly()`函数被声明为virtual函数，因为A和B两个飞机具有相同的默认飞行行为，因此在Airplane类的`fly()`函数中定义这种默认飞行行为，然后让A和B继承。这样的好处是：*
     
     ​    ①    *将所有性质搬到到base class中，然后让两个class继承*
     
     ​    ②    *避免代码重复，并提升未来的强化能力，减缓长期维护所需的成本*
   
   - *但是万一有一个ModelC，不使用这个`fly()`呢，吃瘪了吧，所以要把虚函数改成纯虚函数*
     
     ①     *展示第一种修改方法*
     
     ```cpp
     class Airport {}; //机场
     class Airplane {
     public:
         virtual void fly(const Airport& destination) = 0;
     protected:
         void defaultFly(const Airport& destination) {
             //飞机飞往指定的目的地(默认行为)
         }
     };
     
     class ModelA :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             defaultFly(destination);
         }
     };
     class ModelB :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             defaultFly(destination);
         }
     };
     
     class ModelC :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             //C型飞机不可以使用默认飞行行为，因此定义自己的飞行方式
         }
     };
     ```
     
     *现在C型飞机，或者别的添加的飞机就不会意外继承默认的飞行行为了（因为我们将默认的飞行行为封装到一个defualtFly函数中了），自己可以在`fly()`中定义飞行行为了*
     
     *注意，在A和B的类的`fly()`函数中，对`defaultFly()`做了一个inline调用（见条款30，inline和virtual函数之间的交互关系）*

     ②     *第二种修改方法*
    
     *上面我们将`fly()`接口和实现（`defaultFly()`函数）分开来实现，有些人可能会反对这样做，因为这样会因过度雷同的函数名称而引起class命名空间污染*
    
     *如果不想将上述两个行为分开，那么可以为纯虚函数进行定义，在其中给出`defaultFly()`函数的相关内容。例如：*
    
     ```cpp
     class Airport {}; //机场
     class Airplane {
     public:
         //实现纯虚函数
         virtual void fly(const Airport& destination) = 0 {
             //飞机飞往指定的目的地(默认行为)
         }
     };
    
     class ModelA :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             Airplane::fly(destination);
         }
     };
     class ModelB :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             Airplane::fly(destination);
         }
     };
    
     class ModelC :public Airplane {
     public:
         virtual void fly(const Airport& destination) {
             //定义自己的飞行方式
         }
     };
     ```
    
     *这个设计实现的功能和上面的演示案例是一样的，只不过在派生类的`fly()`函数中用纯虚函数`Airplane::fly`替换了独立函数`Airplane::defaultFly`*
    
     *这种合并行为丧失了“让两个函数享有不同保护级别”的机会：例如上面的`defaultFly()`函数从protected变为了public*

4. *普通的成员函数*
   
   - 最后来看看Airplane的普通成员函数
     
     ```cpp
     class Shape {
     public:
         int objectID()const; //普通成员函数，不希望派生类隐藏
     };
     
     class Rectangle :public Shape {};
     class Ellipse :public Shape {};
     ```
   
   - 设置普通的成员函数的目的：
     
     - 意味着基类不希望派生类去隐藏这个成员函数
     - 实际上一个普通的成员函数所表现的不变性凌驾其特异性，因为它表示不论派生类变得多特特异化，它的行为都不可以改变
   
   - 在上面的代码中：
     
     - 每个Shape对象都有一个用来产生对象识别码的函数
     - 此识别码总是采用相同计算方法，该方法有Shape::objectID的定义式决定，任何派生类都不应该尝试改变其行为
   
   - 由于普通成员函数代表的意义是不变性凌驾特异性，所以它绝不该在派生类中被重新定义（这也是条款36所讨论的一个重点）

### ***Summery***

1. *接口继承和实现继承不同。在public继承之下，derived classes总是继承base class的接口*
2. *pure virtual函数只具体hiding接口继承*
3. *impure virtaul函数具体指定接口继承及缺省实现继承*
4. *non-virtual函数具体指定接口继承以及强制性实现继承*
5. *题外话，是不是光看这个条款还是不懂接口继承和实现继承到底是啥，对吧*

## ***条款35 考虑virtual函数以外的选择***

1. *一般做法*
   
   - *我们都玩过游戏，在砍杀游戏中，我们假定使用成员函数`healthValue`，它会返回一个整数，表示人物的健康程度。将其设置为`virtual`似乎是再明白不过的做法*
     
     ```cpp
     class GameCharacter {
     public:
       virtual int healthValue() const;
       ...
     }
     ```

2. *`Non-Virtual Interface`手法 实现 `Template Method`模式*
   
   - *这里是不是不懂`Teamplate Method`模式，这可不是c++的 template 哦，不懂就去看《[设计模式-模板方法](https://vlicecream.github.io/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95/)》*
   
   - *有一种流派，它主张`virtual`函数应该几乎总是`private`。这个流派的拥护者建议，较好的设计是保留`healthValue`为`public`成员函数，但让它成为`non-virtual`，并调用一个`private virtual`函数*
     
     ```cpp
     class GameCharacter {
     public:
       int healthValue() const {
         ...
         int retValue = doHealthValue;
         ...
         return retValue;
       }
       ...
     private:
       virtual int doHealthValue() const { // derived classes 可重新定义它
         ...  // 缺省算法，计算健康指数
       }
     }
     ```

3. *`Function Pointers`实现 `Strategy` 模式*
   
   - *这里是不是不懂`Strategy`模式，不懂就去看《[设计模式-策略模式](https://vlicecream.github.io/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F-%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F/)》*
   
   - *另一种流派设计主张”人物健康指数的计算与人物类型无关“，这样计算完全不需要”人物“这个成分*
     
     *例如我们可能会要求每个人物的构造函数接受一个指针，指向一个健康计算函数，而我们可以调用该函数进行实际计算*
     
     ```cpp
     class Gamecharacter; // 前置声明
     // 以下函数就是计算健康指数的缺省算法
     int defaultHealthCalc(const GameCharacter& gc);
     class GameCharacter {
     public:
       typedef int (*HealthCalcFunc) (const GameCharacter&);
       explicit GameCharacter(HealthCalcFunc hcf = defaultHealthCalc)
         : healthFunc(hcf) { }
       int healthValue() const { return healthFunc(*this); }
       ...
     private:
       HealthCalcFunc healthFunc;
     }
     ```
     
     *相比于之前做法，该`Strategy`设计模式的简单应用，它提供了某些有趣弹性*

